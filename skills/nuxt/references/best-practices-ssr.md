---
name: ssr-best-practices
description: 避免 SSR 上下文泄漏、水合不匹配和正确的 composable 使用
---

# SSR 最佳实践

避免常见 SSR 陷阱的模式：上下文泄漏、水合不匹配和 composable 错误。

## "Nuxt 实例不可用"错误

当在正确的上下文之外调用 Nuxt composables 时会发生此错误。

### ❌ 错误：在设置之外使用 Composable

```ts
// composables/bad.ts
// 在模块级别调用 - 没有 Nuxt 上下文！
const config = useRuntimeConfig()

export function useMyComposable() {
  return config.public.apiBase
}
```

### ✅ 正确：在函数内部使用 Composable

```ts
// composables/good.ts
export function useMyComposable() {
  // 在 composable 内部调用 - 有上下文
  const config = useRuntimeConfig()
  return config.public.apiBase
}
```

### Composables 的有效上下文

Nuxt composables 在以下环境中工作：
- `<script setup>` 块
- `setup()` 函数
- `defineNuxtPlugin()` 回调
- `defineNuxtRouteMiddleware()` 回调

```ts
// ✅ 插件
export default defineNuxtPlugin(() => {
  const config = useRuntimeConfig() // 有效
})

// ✅ 中间件
export default defineNuxtRouteMiddleware(() => {
  const route = useRoute() // 有效
})
```

## 避免请求之间的状态泄漏

### ❌ 错误：模块级状态

```ts
// utils/bad.ts
let state = null // 在模块间持久化

export function getState() {
  return state
}

export function setState(newState) {
  state = newState
}
```

### ✅ 正确：使用响应式状态

```ts
// utils/good.ts
import { useState } from '#app'

export function useMyState() {
  // 每个调用器创建新状态
  const state = useState('key', () => null)
  
  const setState = (newState) => {
    state.value = newState
  }
  
  return {
    state: readonly(state),
    setState,
  }
}
```

## 正确处理水合

### ❌ 错误：仅客户端功能

```vue
<script setup>
// 使用仅客户端 API 在 SSR 中失败
if (import.meta.client) {
  useHead({ title: '仅客户端标题' })
}
</script>
```

### ✅ 正确：SSR 安全的代码

```vue
<script setup>
// SSR 安全的代码
const isClient = process.client

useHead({
  title: isClient ? '客户端标题' : '服务器标题'
})
</script>
```

## 客户端专用功能

### 正确使用客户端专用功能

```vue
<script setup lang="ts">
// 仅在客户端使用客户端 API
onMounted(() => {
  if (process.client) {
    // 仅客户端逻辑
    window.addEventListener('resize', handleResize)
  }
})
</script>

<template>
  <ClientOnly>
    <p>此内容仅在客户端显示</p>
  </ClientOnly>
</template>
```

## 服务器专用功能

### 正确使用服务器专用功能

```vue
<script setup lang="ts">
// 仅在服务器端使用服务器 API
const data = await $fetch('/api/data')

if (process.server) {
  // 仅服务器逻辑
  process.cacheData = data
}
</script>

<template>
  <div v-if="process.server">
    <p>此内容仅在服务器渲染</p>
  </div>
</template>
```

## 避免水合不匹配

### 使用过程宏

```vue
<script setup lang="ts">
// 使用 process.client 和 process.server 避免不匹配
const isClient = process.client
const isServer = process.server

const greeting = computed(() => {
  return isClient ? '你好客户端！' : '你好服务器！'
})
</script>

<template>
  <div>{{ greeting }}</div>
</template>
```

### 使用条件渲染

```vue
<script setup lang="ts">
// 使用 <ClientOnly> 和 <TeleportToModal> 等组件
import { process } from '@vue/runtime-core'

const showModal = ref(false)

const openModal = () => {
  showModal.value = process.client
}
</script>

<template>
  <button @click="openModal">打开模态框</button>
  
  <ClientOnly>
    <TeleportToModal v-if="showModal">
      <div class="modal">模态框内容</div>
    </TeleportToModal>
  </ClientOnly>
</template>
```

## Composables 最佳实践

### ✅ 在 setup 中调用 Composables

```vue
<script setup lang="ts">
import { useFetch } from '#app'

// ✅ 正确：在 setup 内部调用
const { data } = await useFetch('/api/data')
</script>
```

### ❌ 在组件外部调用

```ts
// ✅ 正确：在函数内部调用
export function useExternalData() {
  const { data } = useFetch('/api/data') // 错误！
  return data
}
```

### ✅ 创建自定义 Composables

```ts
// composables/useCustom.ts
import { useState } from '#app'

export function useCustomData() {
  const state = useState('customData', () => {
    data: null,
    loading: false,
    error: null,
  })

  const fetchData = async () => {
    state.value.loading = true
    try {
      state.value.data = await $fetch('/api/data')
    } catch (err) {
      state.value.error = err
    } finally {
      state.value.loading = false
    }
  }

  return {
    ...state,
    fetchData,
  }
}
```

## 调试 SSR 问题

### 使用过程检查

```ts
// 开发过程中检查环境
const isSSR = import.meta.server
const isCSR = import.meta.client

console.log('当前环境:', {
  ssr: isSSR,
  csr: isCSR,
  dev: import.meta.dev,
})
```

### 使用开发者工具

```vue
<script setup>
// 使用 process.env 进行调试
const debugMode = import.meta.dev

if (debugMode) {
  console.log('调试模式已启用')
  console.log('客户端状态:', process.client)
  console.log('服务器状态:', process.server)
}
</script>
```

## 常见错误和解决方案

| 错误 | 原因 | 解决方案 |
|------|------|----------|
| Nuxt 实例不可用 | 在模块级调用 composable | 在 setup 或函数内部调用 |
| 水合不匹配 | 客户端/服务器代码混淆 | 使用 process.client/server 条件 |
| 状态泄漏 | 在调用器之间共享状态 | 使用 useState 或 createInjectionState |
| 仅客户端失败 | 在 SSR 中使用浏览器 API | 使用 ClientOnly 组件或条件渲染 |

## 水合优化

### 懒水合（Lazy Hydration）

延迟非关键组件的水合以改善交互时间：

```vue
<template>
  <article>
    <h1>{{ post.title }}</h1>
    <p>{{ post.content }}</p>

    <!-- 延迟加载评论直到可见 -->
    <LazyComments hydrate-on-visible :post-id="post.id" />
  </article>
</template>
```

### 水合边界

使用 `<ClientOnly>` 组件处理必须在客户端的内容：

```vue
<template>
  <div>
    <!-- 服务器和客户端都渲染 -->
    <h1>{{ title }}</h1>

    <!-- 仅客户端渲染 -->
    <ClientOnly>
      <ChatWidget />
      <SocialShare />
    </ClientOnly>
  </div>
</template>
```

### 水合回退内容

为客户端内容提供回退：

```vue
<template>
  <ClientOnly fallback="Loading interactive content...">
    <InteractiveMap />
  </ClientOnly>
</template>
```

## 状态管理最佳实践

### 使用 useState 而非 ref

`useState` 在水合时正确共享状态：

```vue
<!-- UserComponent.vue -->
<script setup>
// ✅ 正确：useState 在水合时正确同步
const user = useState('user', () => null)

if (import.meta.client) {
  user.value = getUserFromStorage()
}
</script>

<!-- UserProfile.vue -->
<script setup>
// 访问相同的用户状态
const user = useState('user')
</script>
```

### 使用 createInjectionState

在组件树间共享状态：

```vue
<!-- ParentComponent.vue -->
<script setup>
const state = createInjectionState('shared-state', {
  value: 0,
  increment: () => state.value++,
})
</script>

<template>
  <ChildComponent />
</template>

<!-- ChildComponent.vue -->
<script setup>
const state = useSharedState('shared-state')
</script>

<template>
  <div>Count: {{ state.value }}</div>
  <button @click="state.increment()">Increment</button>
</template>
```

### 避免 Pinia 水合问题

```ts
// stores/user.ts
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
  state: () => ({
    user: null,
  }),
  actions: {
    async fetchUser() {
      // 确保仅在客户端调用
      if (import.meta.client) {
        this.user = await $fetch('/api/user')
      }
    },
  },
})
```

## DOM 和浏览器 API 最佳实践

### 使用 onMounted 访问 DOM

```vue
<script setup>
onMounted(() => {
  // 此时 DOM 已可用
  const el = document.querySelector('.my-element')
  console.log(el)
})
</script>
```

### 避免在 setup 中访问 DOM

```vue
<script setup>
// ❌ 错误：在 SSR 中会失败
const el = document.querySelector('.element')

// ✅ 正确：在 onMounted 中访问
onMounted(() => {
  const el = document.querySelector('.element')
})
</script>
```

### 使用 onBeforeUnmount 清理

```vue
<script setup>
let timer: NodeJS.Timeout

onMounted(() => {
  // 设置定时器
  timer = setInterval(() => {
    console.log('Tick')
  }, 1000)
})

onBeforeUnmount(() => {
  // 清理定时器
  if (timer) {
    clearInterval(timer)
  }
})
</script>
```

## 性能优化

### 懒加载重型组件

```vue
<script setup>
// Lazy 前缀延迟加载
const HeavyChart = defineAsyncComponent(() => import('./HeavyChart.vue'))
</script>

<template>
  <div>
    <h1>Dashboard</h1>

    <!-- 延迟加载直到需要 -->
    <LazyHeavyChart v-if="showChart" />
    <button @click="showChart = true">Show Chart</button>
  </div>
</template>
```

### 使用 Suspense 处理异步组件

```vue
<template>
  <Suspense>
    <template #default>
      <!-- 异步内容 -->
      <AsyncData />
    </template>

    <template #fallback>
      <!-- 加载状态 -->
      <div>Loading...</div>
    </template>
  </Suspense>
</template>
```

### SSR 专用优化

```vue
<script setup>
// 仅在服务器获取数据
const { data } = await useFetch('/api/data', {
  server: true,
  lazy: true,
  getCachedData: (key, nuxtApp) => nuxtApp.payload.data[key],
})

if (import.meta.server) {
  // 服务器端特定逻辑
  console.log('Running on server')
}
</script>
```

## 调试 SSR 问题

### 水合错误追踪

```vue
<script setup>
// 捕获水合错误
onErrorCaptured((err, instance, info) => {
  if (err.message.includes('Hydration')) {
    console.error('Hydration mismatch:', err)
    console.error('Component:', instance)
    console.error('Info:', info)
  }
})
</script>
```

### 开发模式警告

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  experimental: {
    // 启用水合错误警告
    inlineSSRStyles: false,
    // 强制检查水合不匹配
    payloadExtraction: {
      debug: true,
    },
  },
})
```

### 环境检查

```ts
// 在开发时打印环境信息
export default defineNuxtPlugin((nuxtApp) => {
  if (import.meta.dev) {
    console.log({
      mode: import.meta.client ? 'Client' : 'Server',
      dev: import.meta.dev,
      ssr: nuxtApp.ssrContext,
      route: useRoute(),
    })
  }
})
```

## 安全最佳实践

### 防止 XSS

```vue
<script setup>
// ✅ 正确：使用 v-html 时清理
const { data } = await useFetch('/api/content')

// 清理 HTML
const sanitizedContent = computed(() => {
  return DOMPurify.sanitize(data.value)
})
</script>

<template>
  <!-- 仅在必要时使用 v-html -->
  <div v-html="sanitizedContent" />
</template>
```

### 安全的 localStorage 使用

```vue
<script setup>
onMounted(() => {
  // 仅在客户端访问 localStorage
  if (process.client) {
    const token = localStorage.getItem('token')
    // 安全地处理 token
  }
})
</script>
```

## SEO 和元数据

### SSR 友好的 meta 标签

```vue
<script setup>
// ✅ 正确：在 SSR 中设置
useHead({
  title: 'My Page',
  meta: [
    { name: 'description', content: 'Page description' },
    { property: 'og:title', content: 'My Page' },
  ],
})
</script>
```

### 动态 meta 标签

```vue
<script setup>
const route = useRoute()

const { data: post } = await useFetch(`/api/posts/${route.params.id}`)

useHead(() => ({
  title: post.value?.title || 'My Blog',
  meta: [
    { name: 'description', content: post.value?.excerpt },
    { property: 'og:image', content: post.value?.image },
  ],
}))
</script>
```

<!-- 
源码参考：
- https://nuxt.com/docs/guide/directory-structure/server
- https://nuxt.com/docs/guide/going-further/rendering
- https://nuxt.com/docs/api/composables/use-state
-->