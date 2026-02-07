---
title: 防止 SSR 应用程序中的跨请求状态污染
impact: CRITICAL
impactDescription: SSR 中的单例存储在所有服务器请求之间共享状态，可能导致用户数据在请求之间泄漏
type: gotcha
tags: [vue3, ssr, state-management, pinia, vuex, security, server-side-rendering, nuxt]
---

# 防止 SSR 应用程序中的跨请求状态污染

**影响：严重** - 在服务器端渲染（SSR）应用程序中，单例存储模式会创建一个在所有服务器请求之间共享的实例。这意味着来自一个用户请求的数据可能会泄漏到另一个用户的响应中，导致严重的安全和数据完整性问题。

这是 Vue 状态管理中最关键的陷阱之一，可能会产生严重的生产后果。

## 任务清单

- [ ] 永远不要在 SSR 应用程序中使用单例存储模式
- [ ] 使用 SSR 时为每个请求创建新的存储实例
- [ ] 使用正确处理 SSR 状态管理的 Pinia
- [ ] 使用并发请求测试 SSR 状态隔离
- [ ] 审查任何全局响应式状态的 SSR 兼容性

## 问题：SSR 中的单例状态

```javascript
// store.js - 对 SSR 来说很危险
import { reactive } from 'vue'

// 这是一个单例 - 所有请求使用相同的实例
export const store = reactive({
  user: null,
  cart: [],
  preferences: {}
})
```

**在 SSR 中会发生什么：**

1. 用户 A 的请求 A 到达
2. 服务器设置 `store.user = userA`
3. 在响应完成之前，用户 B 的请求 B 到达
4. 请求 B 看到的是 `store.user = userA`（用户 A 的数据泄漏！）
5. 服务器设置 `store.user = userB`
6. 请求 A 的响应现在可能包含用户 B 的数据

这会导致不可预测的行为和潜在的安全漏洞。

## 解决方案 1：使用 Pinia（推荐）

Pinia 通过为每个请求创建新的存储实例来正确处理 SSR：

```javascript
// stores/user.js
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
  state: () => ({
    user: null,
    preferences: {}
  }),
  actions: {
    setUser(user) {
      this.user = user
    }
  }
})
```

```javascript
// main.js（或 entry-server.js）
import { createPinia } from 'pinia'
import { createApp } from 'vue'
import App from './App.vue'

// 对于 SSR：每个请求创建新实例
export function createAppInstance() {
  const app = createApp(App)
  const pinia = createPinia()

  app.use(pinia)

  return { app, pinia }
}
```

```javascript
// entry-server.js
import { createAppInstance } from './main'
import { renderToString } from 'vue/server-renderer'

export async function render(url, context) {
  // 每个请求新的应用和存储实例
  const { app, pinia } = createAppInstance()

  // ... 设置路由器、获取数据等

  const html = await renderToString(app)

  // 序列化状态以供客户端水合
  const state = pinia.state.value

  return { html, state }
}
```

```javascript
// entry-client.js - 从序列化状态水合
import { createAppInstance } from './main'

const { app, pinia } = createAppInstance()

// 在挂载之前恢复服务器状态
if (window.__PINIA_STATE__) {
  pinia.state.value = window.__PINIA_STATE__
}

app.mount('#app')
```

## 解决方案 2：手动状态管理的工厂模式

如果不使用 Pinia，请创建工厂函数：

```javascript
// store.js - 使用工厂的 SSR 安全
import { reactive, readonly } from 'vue'

// 工厂函数每次调用创建新状态
export function createStore() {
  const state = reactive({
    user: null,
    cart: [],
    preferences: {}
  })

  return {
    state: readonly(state),
    setUser(user) {
      state.user = user
    },
    addToCart(item) {
      state.cart.push(item)
    }
  }
}
```

```javascript
// entry-server.js
import { createStore } from './store'
import { provide } from 'vue'

export async function render(url) {
  const app = createApp(App)

  // 仅为此请求创建新存储实例
  const store = createStore()
  app.provide('store', store)

  // ... 渲染
}
```

## 解决方案 3：基于上下文的状态（高级）

对于像 Nuxt 这样的框架，使用请求上下文：

```javascript
// composables/useRequestState.js
import { useSSRContext } from 'vue'

export function useRequestState(key, initialValue) {
  if (import.meta.env.SSR) {
    const ctx = useSSRContext()
    ctx.state = ctx.state || {}

    if (!(key in ctx.state)) {
      ctx.state[key] = initialValue()
    }

    return ctx.state[key]
  }

  // 客户端：使用常规响应式状态
  return reactive(initialValue())
}
```

## Nuxt.js 自动处理此问题

在 Nuxt 3 中，状态隔离是自动处理的：

```javascript
// Nuxt 自动为每个请求创建新的 Pinia 实例
// 你可以正常使用存储

export default defineNuxtPlugin(async (nuxtApp) => {
  const userStore = useUserStore()
  await userStore.fetchUser()
})
```

## 测试状态污染

```javascript
// test/ssr-state-isolation.test.js
import { describe, it, expect } from 'vitest'
import { render } from './entry-server'

describe('SSR 状态隔离', () => {
  it('不应在并发请求之间泄漏状态', async () => {
    // 模拟并发请求
    const [result1, result2] = await Promise.all([
      render('/user/1', { userId: '1' }),
      render('/user/2', { userId: '2' })
    ])

    // 每个应该有自己的用户数据
    expect(result1.html).toContain('用户 1')
    expect(result2.html).toContain('用户 2')

    // 状态不应混合
    expect(result1.html).not.toContain('用户 2')
    expect(result2.html).not.toContain('用户 1')
  })
})
```

```javascript
// 替代方案：直接测试存储隔离
import { createApp } from './app.js'

test('请求不共享状态', async () => {
  // 模拟两个并发请求
  const { app: app1, store: store1 } = createApp()
  const { app: app2, store: store2 } = createApp()

  store1.user = { id: 1, name: 'Alice' }
  store2.user = { id: 2, name: 'Bob' }

  // 每个应该有自己的状态
  expect(store1.user.name).toBe('Alice')
  expect(store2.user.name).toBe('Bob')
})
```

## 需要注意的危险信号

```javascript
// 任何模块级响应式状态在 SSR 中都是危险的

// 错误：模块级响应式
export const globalUser = ref(null)

// 错误：模块级响应式对象
export const appState = reactive({})

// 错误：共享的 Map/Set
export const cache = new Map()

// 错误：即使是普通对象也可能有问题
let requestCount = 0  // 在请求之间共享
```

## 为什么推荐 Pinia 用于 SSR

1. **自动请求隔离** - 每个请求的新存储实例
2. **内置状态序列化** - 客户端上的简单水合
3. **DevTools 支持** - 在服务器和客户端上调试状态
4. **TypeScript 支持** - 类型安全的状态管理
5. **经过测试的模式** - 经过实战测试的 SSR 处理

## 参考
- [Vue.js 状态管理 - SSR 考量](https://vuejs.org/guide/scaling-up/state-management.html#ssr-considerations)
- [Pinia SSR 指南](https://pinia.vuejs.org/ssr/)
- [Vue SSR 指南](https://vuejs.org/guide/scaling-up/ssr.html)
