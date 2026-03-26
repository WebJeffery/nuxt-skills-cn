---
category: Reactivity
alias: autoResetRef
---

# refAutoReset

一个 ref,它将在一段时间后重置为默认值。

## 用法

```ts
import { refAutoReset } from '@vueuse/core'

const message = refAutoReset('default message', 1000)

function setMessage() {
  // 这里,value 将更改为 'message has set',但在 1000ms 后,它将更改为 'default message'
  message.value = 'message has set'
}
```

::: info
您可以在对内部值进行深度更改后重新分配整个对象以触发更新。

[了解更多关于浅 refs →](https://vuejs.org/api/reactivity-advanced#shallowref)
:::

## 类型声明

```ts
export type RefAutoResetReturn<T = any> = Ref<T>
/**
 * 创建一个 ref,它将在一段时间后重置为默认值。
 *
 * @see https://vueuse.org/refAutoReset
 * @param defaultValue 将设置的值。
 * @param afterMs      以毫秒为单位的零或更大的延迟。
 */
export declare function refAutoReset<T>(
  defaultValue: MaybeRefOrGetter<T>,
  afterMs?: MaybeRefOrGetter<number>,
): RefAutoResetReturn<T>
/** @deprecated 使用 `refAutoReset` 代替 */
export declare const autoResetRef: typeof refAutoReset
```
