---
category: '@Math'
---

# useClamp

响应式将值限制在两个其他值之间。

## 用法

```ts
import { useClamp } from '@vueuse/math'

const min = shallowRef(0)
const max = shallowRef(10)
const value = useClamp(0, min, max)
```

### 可写 Ref

当您传递可变 `ref` 时,返回的值是一个**可写计算属性**,在设置时限制值:

```ts
import { useClamp } from '@vueuse/math'

const number = shallowRef(0)
const clamped = useClamp(number, 0, 10)

clamped.value = 15 // clamped.value 将为 10
clamped.value = -5 // clamped.value 将为 0
```

### 只读模式

当您传递 getter 函数或只读 ref 时,返回的值是一个只读计算属性:

```ts
import { useClamp } from '@vueuse/math'

const value = ref(5)
const clamped = useClamp(() => value.value * 2, 0, 10)

// clamped.value 是从 getter 计算的
```

### 响应式边界

所有参数(value、min、max)都可以是响应式的:

```ts
import { useClamp } from '@vueuse/math'

const value = shallowRef(5)
const min = shallowRef(0)
const max = shallowRef(10)

const clamped = useClamp(value, min, max)

max.value = 3 // clamped.value 自动变为 3
```

## 类型声明

```ts
/**
 * 响应式将值限制在两个其他值之间。
 *
 * @see https://vueuse.org/useClamp
 * @param value number
 * @param min
 * @param max
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useClamp(
  value: ReadonlyRefOrGetter<number>,
  min: MaybeRefOrGetter<number>,
  max: MaybeRefOrGetter<number>,
): ComputedRef<number>
export declare function useClamp(
  value: MaybeRefOrGetter<number>,
  min: MaybeRefOrGetter<number>,
  max: MaybeRefOrGetter<number>,
): Ref<number>
```
