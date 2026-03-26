---
category: Browser
---

# useTextDirection

元素文本的响应式 [dir](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/dir)。

## 用法

```ts
import { useTextDirection } from '@vueuse/core'

const dir = useTextDirection() // Ref<'ltr' | 'rtl' | 'auto'>
```

默认情况下，当 `html` 标签应用了 dir `rtl` 时，它返回 `rtl` 方向，例如：

```html
<!--ltr-->
<html>
  ...
</html>

<!--rtl-->
<html dir="rtl">
  ...
</html>
```

## 选项

```ts
import { useTextDirection } from '@vueuse/core'

const mode = useTextDirection({
  selector: 'body'
}) // Ref<'ltr' | 'rtl' | 'auto'>
```

## 类型声明

```ts
export type UseTextDirectionValue = "ltr" | "rtl" | "auto"
export interface UseTextDirectionOptions extends ConfigurableDocument {
  /**
   * 要应用到的目标元素的 CSS 选择器
   *
   * @default 'html'
   */
  selector?: string
  /**
   * 使用 MutationObserve 观察 `document.querySelector(selector)` 更改
   *
   * @default false
   */
  observe?: boolean
  /**
   * 初始值
   *
   * @default 'ltr'
   */
  initialValue?: UseTextDirectionValue
}
/**
 * 元素文本的响应式 dir。
 *
 * @see https://vueuse.org/useTextDirection
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useTextDirection(
  options?: UseTextDirectionOptions,
): WritableComputedRef<UseTextDirectionValue, UseTextDirectionValue>
```
