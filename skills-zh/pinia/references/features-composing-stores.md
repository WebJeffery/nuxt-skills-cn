---
name: composing-stores
description: Store 间通信和避免循环依赖
---

# 组合 Stores

Stores 可以互相使用以共享状态和逻辑。

## 规则: 避免循环依赖

两个 store 不能在初始化期间直接读取彼此的状态:

```ts
// ❌ 无限循环
const useX = defineStore('x', () => {
  const y = useY()
  y.name // 不要在这里读取!
  return { name: ref('X') }
})

const useY = defineStore('y', () => {
  const x = useX()
  x.name // 不要在这里读取!
  return { name: ref('Y') }
})
```

**解决方案:** 在 getters、computed 或 actions 中读取:

```ts
const useX = defineStore('x', () => {
  const y = useY()

  // ✅ 在 computed/actions 中读取
  function doSomething() {
    const yName = y.name
  }

  return { name: ref('X'), doSomething }
})
```

## Setup Stores: 在顶部使用 Store

```ts
import { defineStore } from 'pinia'
import { useUserStore } from './user'

export const useCartStore = defineStore('cart', () => {
  const user = useUserStore()
  const list = ref([])

  const summary = computed(() => {
    return `Hi ${user.name}, you have ${list.value.length} items`
  })

  function purchase() {
    return apiPurchase(user.id, list.value)
  }

  return { list, summary, purchase }
})
```

## 共享 Getters

在 getter 中调用 `useStore()`:

```ts
import { useUserStore } from './user'

export const useCartStore = defineStore('cart', {
  getters: {
    summary(state) {
      const user = useUserStore()
      return `Hi ${user.name}, you have ${state.list.length} items`
    },
  },
})
```

## 共享 Actions

在 action 中调用 `useStore()`:

```ts
import { useUserStore } from './user'
import { apiOrderCart } from './api'

export const useCartStore = defineStore('cart', {
  actions: {
    async orderCart() {
      const user = useUserStore()

      try {
        await apiOrderCart(user.token, this.items)
        this.emptyCart()
      } catch (err) {
        displayError(err)
      }
    },
  },
})
```

## SSR: 在 Await 之前调用 Stores

在异步 actions 中，在任何 `await` 之前调用所有 stores:

```ts
actions: {
  async orderCart() {
    // ✅ 在 await 之前所有 useStore() 调用
    const user = useUserStore()
    const analytics = useAnalyticsStore()

    try {
      await apiOrderCart(user.token, this.items)
      // ❌ 不要在 await 之后调用 useStore() (SSR 问题)
      // const otherStore = useOtherStore()
    } catch (err) {
      displayError(err)
    }
  },
}
```

这确保在 SSR 期间使用正确的 Pinia 实例。

<!--
源引用:
- https://pinia.vuejs.org/cookbook/composing-stores.html
-->
