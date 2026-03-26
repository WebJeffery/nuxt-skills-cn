---
category: Reactivity
---

# refManualReset

创建一个具有手动重置功能的 ref。

## 用法

```ts
import { refManualReset } from '@vueuse/core'

const message = refManualReset('default message')

message.value = 'message has set'

message.reset()

console.log(message.value) // 'default message'
```

## 类型声明

```ts
/**
 * 定义支持手动重置功能的 ref 的形状。
 *
 * 此接口扩展了 Vue 的标准 `Ref` 类型并添加了 `reset` 方法。
 * `reset` 方法允许 ref 手动重置为其默认值。
 */
export interface ManualResetRefReturn<T> extends Ref<T> {
  reset: Fn
}
/**
 * 创建一个具有手动重置功能的 ref。
 *
 * @see https://vueuse.org/refManualReset
 * @param defaultValue 将设置的值。
 */
export declare function refManualReset<T>(
  defaultValue: MaybeRefOrGetter<T>,
): ManualResetRefReturn<T>
```
