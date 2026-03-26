---
category: Reactivity
---

# reactiveOmit

从响应式对象中响应式地省略字段。

## 用法

### 基本用法

```ts
import { reactiveOmit } from '@vueuse/core'

const obj = reactive({
  x: 0,
  y: 0,
  elementX: 0,
  elementY: 0,
})

const picked = reactiveOmit(obj, 'x', 'elementX') // { y: number, elementY: number }
```

### 谓词用法

```ts
import { reactiveOmit } from '@vueuse/core'

const obj = reactive({
  bar: 'bar',
  baz: 'should be omit',
  foo: 'foo2',
  qux: true,
})

const picked = reactiveOmit(obj, (value, key) => key === 'baz' || value === true)
// { bar: string, foo: string }
```

### 场景

#### 选择性地将 props 传递给子组件

```vue
<script setup lang="ts">
import { reactiveOmit } from '@vueuse/core'

const props = defineProps<{
  value: string
  color?: string
  font?: string
}>()

const childProps = reactiveOmit(props, 'value')
</script>

<template>
  <div>
    <!-- 只将 "color" 和 "font" props 传递给子组件 -->
    <ChildComp v-bind="childProps" />
  </div>
</template>
```

## 类型声明

```ts
export type ReactiveOmitReturn<
  T extends object,
  K extends keyof T | undefined = undefined,
> = [K] extends [undefined] ? Partial<T> : Omit<T, Extract<K, keyof T>>
export type ReactiveOmitPredicate<T> = (
  value: T[keyof T],
  key: keyof T,
) => boolean
export declare function reactiveOmit<T extends object, K extends keyof T>(
  obj: T,
  ...keys: (K | K[])[]
): ReactiveOmitReturn<T, K>
export declare function reactiveOmit<T extends object>(
  obj: T,
  predicate: ReactiveOmitPredicate<T>,
): ReactiveOmitReturn<T>
```
