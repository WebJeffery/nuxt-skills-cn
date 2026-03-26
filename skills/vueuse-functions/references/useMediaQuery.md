---
category: Browser
---

# useMediaQuery

响应式 [Media Query](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Testing_media_queries)。一旦创建了 MediaQueryList 对象，您就可以检查查询的结果或在结果更改时接收通知。

## 用法

```ts
import { useMediaQuery } from '@vueuse/core'

const isLargeScreen = useMediaQuery('(min-width: 1024px)')
const isPreferredDark = useMediaQuery('(prefers-color-scheme: dark)')
```

#### 服务端渲染和 Nuxt

如果您在启用 SSR 的情况下使用 `useMediaQuery`，则需要指定您希望在服务器上和水合之前渲染的屏幕尺寸，以避免水合不匹配

```ts
import { useMediaQuery } from '@vueuse/core'

const isLarge = useMediaQuery('(min-width: 1024px)', {
  ssrWidth: 768 // 将启用 SSR 模式，并像屏幕宽度为 768px 一样渲染
})

console.log(isLarge.value) // 始终为 false，因为 768px 的 ssrWidth 小于 1024px
onMounted(() => {
  console.log(isLarge.value) // 如果屏幕小于 1024px 则为 false，如果大于 1024px 则为 true
})
```

或者，您可以使用 [`provideSSRWidth`](../useSSRWidth/index.md) 为您的应用程序全局设置此功能

## Type Declarations

```ts
/**
 * Reactive Media Query.
 *
 * @see https://vueuse.org/useMediaQuery
 * @param query
 * @param options
 */
export declare function useMediaQuery(
  query: MaybeRefOrGetter<string>,
  options?: ConfigurableWindow & {
    ssrWidth?: number
  },
): ComputedRef<boolean>
```
