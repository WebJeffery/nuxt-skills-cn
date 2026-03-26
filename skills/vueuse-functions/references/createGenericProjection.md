---
category: '@Math'
---

# createGenericProjection

`createProjection` 的通用版本。接受自定义投影函数来映射任意类型的域。

参考 `createProjection` 和 `useProjection`

## 类型声明

```ts
export type ProjectorFunction<F, T> = (
  input: F,
  from: readonly [F, F],
  to: readonly [T, T],
) => T
export type UseProjection<F, T> = (input: MaybeRefOrGetter<F>) => ComputedRef<T>
export declare function createGenericProjection<F = number, T = number>(
  fromDomain: MaybeRefOrGetter<readonly [F, F]>,
  toDomain: MaybeRefOrGetter<readonly [T, T]>,
  projector: ProjectorFunction<F, T>,
): UseProjection<F, T>
```
