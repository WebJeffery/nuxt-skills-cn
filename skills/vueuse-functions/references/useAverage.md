---
category: '@Math'
---

# useAverage

响应式获取数组的平均值。

## 用法

```ts
import { useAverage } from '@vueuse/math'

const list = ref([1, 2, 3])
const averageValue = useAverage(list) // Ref<2>
```

```ts
import { useAverage } from '@vueuse/math'

const a = ref(1)
const b = ref(3)

const averageValue = useAverage(a, b) // Ref<2>
```

## 类型声明

```ts
export declare function useAverage(
  array: MaybeRefOrGetter<MaybeRefOrGetter<number>[]>,
): ComputedRef<number>
export declare function useAverage(
  ...args: MaybeRefOrGetter<number>[]
): ComputedRef<number>
```
