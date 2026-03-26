---
category: Reactivity
---

# reactivePick

从响应式对象中响应式地选择字段。

## 用法

### 基本用法

```ts
import { reactivePick } from '@vueuse/core'

const obj = reactive({
  x: 0,
  y: 0,
  elementX: 0,
  elementY: 0,
})

const picked = reactivePick(obj, 'x', 'elementX') // { x: number, elementX: number }
```

### 谓词用法

```ts
import { reactivePick } from '@vueuse/core'

const source = reactive({
  foo: 'foo',
  bar: 'bar',
  baz: 'baz',
  qux: true,
})
const state = reactivePick(source, (value, key) => key !== 'bar' && value !== true)
// { foo: string, baz: string }
source.qux = false
// { foo: string, baz: string, qux: boolean }
```

### 场景

#### 选择性地将 props 传递给子组件

```vue
<script setup lang="ts">
import { reactivePick } from '@vueuse/core'

const props = defineProps<{
  value: string
  color?: string
  font?: string
}>()

const childProps = reactivePick(props, 'color', 'font')
</script>

<template>
  <div>
    <!-- 只将 "color" 和 "font" props 传递给子组件 -->
    <ChildComp v-bind="childProps" />
  </div>
</template>
```

#### 选择性地包装响应式对象

与其这样做

```ts
import { useElementBounding } from '@vueuse/core'
import { reactive } from 'vue'

const { height, width } = useElementBounding() // ref 对象
const size = reactive({ height, width })
```

现在我们可以这样做

```ts
import { reactivePick, useElementBounding } from '@vueuse/core'

const size = reactivePick(useElementBounding(), 'height', 'width')
```

## 类型声明

```ts
export type ReactivePickReturn<T extends object, K extends keyof T> = {
  [S in K]: UnwrapRef<T[S]>
}
export type ReactivePickPredicate<T> = (
  value: T[keyof T],
  key: keyof T,
) => boolean
export declare function reactivePick<T extends object, K extends keyof T>(
  obj: T,
  ...keys: (K | K[])[]
): ReactivePickReturn<T, K>
export declare function reactivePick<T extends object>(
  obj: T,
  predicate: ReactivePickPredicate<T>,
): ReactivePickReturn<T, keyof T>
```
