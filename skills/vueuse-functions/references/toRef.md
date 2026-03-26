---
category: Reactivity
alias: resolveRef
---

# toRef

将值/ref/getter 规范化为 `ref` 或 `computed`。

## 用法

```ts
import { toRef } from '@vueuse/core'

const foo = ref('hi')

const a = toRef(0) // Ref<number>
const b = toRef(foo) // Ref<string>
const c = toRef(() => 'hi') // ComputedRef<string>
```

## 与 Vue 的 `toRef` 的区别

VueUse 的 `toRef` 与 `vue` 包中的 Vue 的 `toRef` 不同。

### VueUse `toRef`

- 接受 **值**、**ref** 或 **getter**
- 返回:
  - 原始值的 **ref**
  - 现有 ref 的 **ref**
  - getter 函数的 **computed**
- **不**接受 `object + key`
- Getter 总是产生只读的 computed 值

### Vue `toRef`

- 仅接受:
  - **响应式对象 + 属性键**,或
  - 现有的 **ref**
- 产生链接到底层响应式对象的 **可写 ref**
- **不**接受原始值
- **不**接受 getter 函数

### 总结

| 行为                     | VueUse `toRef`            | Vue `toRef`             |
| ------------------------ | ------------------------- | ----------------------- |
| 接受原始值               | ✔️                        | ❌                      |
| 接受 getter              | ✔️ (computed)             | ❌                      |
| 接受现有 ref             | ✔️                        | ✔️                      |
| 接受 object + key        | ❌                        | ✔️                      |
| 可写                     | ✔️ (除了 getter)          | ✔️                      |
| 目的                     | 规范化为 ref/computed     | 绑定到响应式对象        |

## 类型声明

```ts
/**
 * 将值/ref/getter 规范化为 `ref` 或 `computed`。
 */
export declare function toRef<T>(r: () => T): Readonly<Ref<T>>
export declare function toRef<T>(r: ComputedRef<T>): ComputedRef<T>
export declare function toRef<T>(r: MaybeRefOrGetter<T>): Ref<T>
export declare function toRef<T>(r: T): Ref<T>
export declare function toRef<T extends object, K extends keyof T>(
  object: T,
  key: K,
): ToRef<T[K]>
export declare function toRef<T extends object, K extends keyof T>(
  object: T,
  key: K,
  defaultValue: T[K],
): ToRef<Exclude<T[K], undefined>>
```
