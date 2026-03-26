---
name: nuxt-integration
description: 在 Nuxt 中使用 Pinia - 自动导入、SSR 和最佳实践
---

# Nuxt 集成

Pinia 与 Nuxt 3/4 无缝协作，自动处理 SSR、序列化和 XSS 保护。

## 安装

```bash
npx nuxi@latest module add pinia
```

这将同时安装 `@pinia/nuxt` 和 `pinia`。如果 `pinia` 未安装，请手动添加。

> **npm 用户:** 如果遇到 `ERESOLVE unable to resolve dependency tree`，添加到 `package.json`:
> ```json
> "overrides": { "vue": "latest" }
> ```

## 配置

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],
})
```

## 自动导入

这些功能自动可用:
- `usePinia()` - 获取 pinia 实例
- `defineStore()` - 定义 store
- `storeToRefs()` - 提取响应式 ref
- `acceptHMRUpdate()` - HMR 支持

**`app/stores/` (Nuxt 4) 或 `stores/` 中的所有 store 都会自动导入。**

### 自定义 Store 目录

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@pinia/nuxt'],
  pinia: {
    storesDirs: ['./stores/**', './custom-folder/stores/**'],
  },
})
```

## 在页面中获取数据

使用 `callOnce()` 进行 SSR 友好的数据获取:

```vue
<script setup>
const store = useStore()

// 运行一次，数据在导航间持久化
await callOnce('user', () => store.fetchUser())
</script>
```

### 导航时重新获取

```vue
<script setup>
const store = useStore()

// 每次导航时重新获取 (类似 useFetch)
await callOnce('user', () => store.fetchUser(), { mode: 'navigation' })
</script>
```

## 在组件外使用 Stores

在导航守卫、中间件或其他 store 中，传递 `pinia` 实例:

```ts
// middleware/auth.ts
export default defineNuxtRouteMiddleware((to) => {
  const nuxtApp = useNuxtApp()
  const store = useStore(nuxtApp.$pinia)

  if (to.meta.requiresAuth && !store.isLoggedIn) {
    return navigateTo('/login')
  }
})
```

大多数情况下，你不需要这样做 - 只需在组件或其他支持注入的上下文中使用 store。

## 使用 Nuxt 的 Pinia 插件

创建一个 Nuxt 插件:

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
- https://pinia.vuejs.org/ssr/nuxt.html
-->
