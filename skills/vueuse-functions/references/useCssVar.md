---
category: Browser
---

# useCssVar

操作 CSS 变量

## 用法

```ts
import { useCssVar } from '@vueuse/core'
import { useTemplateRef } from 'vue'

const el = useTemplateRef('el')
const color1 = useCssVar('--color', el)

const elv = useTemplateRef('elv')
const key = ref('--color')
const colorVal = useCssVar(key, elv)

const someEl = useTemplateRef('someEl')
const color2 = useCssVar('--color', someEl, { initialValue: '#eee' })
```

## 类型声明

```ts
export interface UseCssVarOptions extends ConfigurableWindow {
  initialValue?: string
  /**
   * 使用 MutationObserver 监视变量更改
   * @default false
   */
  observe?: boolean
}
/**
 * 操作 CSS 变量。
 *
 * @see https://vueuse.org/useCssVar
 * @param prop
 * @param target
 * @param options
 */
export declare function useCssVar(
  prop: MaybeRefOrGetter<string | null | undefined>,
  target?: MaybeElementRef,
  options?: UseCssVarOptions,
): ShallowRef<string | undefined, string | undefined>
```
