---
category: Browser
---

# useTitle

响应式文档标题。

::: warning
此组合式函数与 SSR 不兼容。
:::

## 用法

```ts
import { useTitle } from '@vueuse/core'

const title = useTitle()
console.log(title.value) // 打印当前标题
title.value = 'Hello' // 更改当前标题
```

立即设置初始标题：

```ts
import { useTitle } from '@vueuse/core'
// ---cut---
const title = useTitle('New Title')
```

传递一个 `ref`，当源 ref 更改时标题将更新：

```ts
import { useTitle } from '@vueuse/core'
import { shallowRef } from 'vue'

const messages = shallowRef(0)

const title = computed(() => {
  return !messages.value ? 'No message' : `${messages.value} new messages`
})

useTitle(title) // 文档标题将与 ref "title" 匹配
```

传递一个可选的模板标签 [Vue Meta Title Template](https://vue-meta.nuxtjs.org/guide/metainfo.html) 以更新要注入到此模板的标题：

```ts
import { useTitle } from '@vueuse/core'
// ---cut---
const title = useTitle('New Title', {
  titleTemplate: '%s | My Awesome Website'
})
```

::: warning
`observe` 与 `titleTemplate` 不兼容。
:::

## 类型声明

```ts
export type UseTitleOptionsBase = {
  /**
   * 在卸载时恢复原始标题
   * @param originTitle 原始标题
   * @returns 恢复的标题
   */
  restoreOnUnmount?:
    | false
    | ((
        originalTitle: string,
        currentTitle: string,
      ) => string | null | undefined)
} & (
  | {
      /**
       * 使用 MutationObserve 观察 `document.title` 更改
       * 不能与 `titleTemplate` 选项一起使用。
       *
       * @default false
       */
      observe?: boolean
    }
  | {
      /**
       * 用于解析标题的模板字符串（例如，'%s | My Website'）
       * 不能与 `observe` 选项一起使用。
       *
       * @default '%s'
       */
      titleTemplate?: MaybeRef<string> | ((title: string) => string)
    }
)
export type UseTitleOptions = ConfigurableDocument & UseTitleOptionsBase
export type UseTitleReturn =
  | ComputedRef<string | null | undefined>
  | Ref<string | null | undefined>
/**
 * 响应式文档标题。
 *
 * @see https://vueuse.org/useTitle
 * @param newTitle
 * @param options
 * @description 它与 SSR 不兼容。您的值将仅在客户端应用。
 */
export declare function useTitle(
  newTitle: ReadonlyRefOrGetter<string | null | undefined>,
  options?: UseTitleOptions,
): ComputedRef<string | null | undefined>
export declare function useTitle(
  newTitle?: MaybeRef<string | null | undefined>,
  options?: UseTitleOptions,
): Ref<string | null | undefined>
```
