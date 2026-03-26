---
category: Browser
---

# useStyleTag

将样式标签注入到文档中。

## 用法

```ts
import { useStyleTag } from '@vueuse/core'

const { id, load, unload, isLoaded } = useStyleTag('.foo { color: red; }')

load()
```

## 类型声明

```ts
export interface UseStyleTagOptions extends ConfigurableDocument {
  /**
   * 样式标签的媒体属性
   */
  media?: string
  /**
   * 样式标签的 nonce 属性
   */
  nonce?: string
  /**
   * 样式标签的 id 属性
   */
  id?: string
  /**
   * 样式标签的 type 属性
   *
   * @default 'text/css'
   */
  type?: string
}
export interface UseStyleTagReturn {
  id: string
  css: Ref<string>
  load: (css: string, options?: UseStyleTagOptions) => void
  unload: () => void
  isLoaded: Ref<boolean>
}
/**
 * 将样式标签注入到文档中。
 *
 * @see https://vueuse.org/useStyleTag
 * @param css
 * @param options
 */
export declare function useStyleTag(
  css: MaybeRefOrGetter<string>,
  options?: UseStyleTagOptions,
): UseStyleTagReturn
```
