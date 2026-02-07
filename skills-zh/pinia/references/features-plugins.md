---
name: plugins
description: 使用自定义属性、方法和行为扩展 store
---

# 插件

插件使用自定义属性、方法或行为扩展所有 store。

## 基本插件

```ts
import { createPinia } from 'pinia'

function SecretPiniaPlugin() {
  return { secret: 'the cake is a lie' }
}

const pinia = createPinia()
pinia.use(SecretPiniaPlugin)

// 在任何 store 中
const store = useStore()
store.secret // 'the cake is a lie'
```

## 插件上下文

插件接收一个上下文对象:

```ts
import { PiniaPluginContext } from 'pinia'

export function myPiniaPlugin(context: PiniaPluginContext) {
  context.pinia   // pinia 实例
  context.app     // Vue 应用实例
  context.store   // 被增强的 store
  context.options // store 定义选项
}
```

## 添加属性

返回一个对象来添加属性 (在 devtools 中跟踪):

```ts
pinia.use(() => ({ hello: 'world' }))
```

或直接在 store 上设置:

```ts
pinia.use(({ store }) => {
  store.hello = 'world'
  // 在开发模式下获得 devtools 可见性
  if (process.env.NODE_ENV === 'development') {
    store._customProperties.add('hello')
  }
})
```

## 添加状态

添加到 `store` 和 `store.$state` 以支持 SSR/devtools:

```ts
import { toRef, ref } from 'vue'

pinia.use(({ store }) => {
  if (!store.$state.hasOwnProperty('hasError')) {
    const hasError = ref(false)
    store.$state.hasError = hasError
  }
  store.hasError = toRef(store.$state, 'hasError')
})
```

## 添加外部属性

使用 `markRaw()` 包装非响应式对象:

```ts
import { markRaw } from 'vue'
import { router } from './router'

pinia.use(({ store }) => {
  store.router = markRaw(router)
})
```

## 自定义 Store 选项

定义由插件使用的自定义选项:

```ts
// Store 定义
defineStore('search', {
  actions: {
    searchContacts() { /* ... */ },
  },
  debounce: {
    searchContacts: 300,
  },
})

// 插件读取自定义选项
import debounce from 'lodash/debounce'

pinia.use(({ options, store }) => {
  if (options.debounce) {
    return Object.keys(options.debounce).reduce((acc, action) => {
      acc[action] = debounce(store[action], options.debounce[action])
      return acc
    }, {})
  }
})
```

对于 Setup Stores，将选项作为第三个参数传递:

```ts
defineStore(
  'search',
  () => { /* ... */ },
  {
    debounce: { searchContacts: 300 },
  }
)
```

## TypeScript 增强

### 自定义属性

```ts
import 'pinia'
import type { Router } from 'vue-router'

declare module 'pinia' {
  export interface PiniaCustomProperties {
    router: Router
    hello: string
  }
}
```

### 自定义状态

```ts
declare module 'pinia' {
  export interface PiniaCustomStateProperties<S> {
    hasError: boolean
  }
}
```

### 自定义选项

```ts
declare module 'pinia' {
  export interface DefineStoreOptionsBase<S, Store> {
    debounce?: Partial<Record<keyof StoreActions<Store>, number>>
  }
}
```

## 在插件中订阅

```ts
pinia.use(({ store }) => {
  store.$subscribe(() => {
    // 响应状态变化
  })
  store.$onAction(() => {
    // 响应 actions
  })
})
```

## Nuxt 插件

创建一个 Nuxt 插件来添加 Pinia 插件:

```ts
// plugins/myPiniaPlugin.ts
import { PiniaPluginContext } from 'pinia'

function MyPiniaPlugin({ store }: PiniaPluginContext) {
  store.$subscribe((mutation) => {
    console.log(`[🍍 ${mutation.storeId}]: ${mutation.type}`)
  })
  return { creationTime: new Date() }
}

export default defineNuxtPlugin(({ $pinia }) => {
  $pinia.use(MyPiniaPlugin)
})
```

<!--
源引用:
- https://pinia.vuejs.org/core-concepts/plugins.html
-->
