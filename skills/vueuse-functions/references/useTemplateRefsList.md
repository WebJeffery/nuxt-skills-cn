---
category: Component
---

# useTemplateRefsList

在 `v-for` 中将 refs 绑定到模板元素和组件的简写。

## 用法

```vue
<script setup lang="ts">
import { useTemplateRefsList } from '@vueuse/core'
import { onUpdated } from 'vue'

const refs = useTemplateRefsList<HTMLDivElement>()

onUpdated(() => {
  console.log(refs)
})
</script>

<template>
  <div v-for="i of 5" :key="i" :ref="refs.set" />
</template>
```

## 类型声明

```ts
export type TemplateRefsList<T> = T[] & {
  set: (el: object | null) => void
}
export declare function useTemplateRefsList<T = Element>(): Readonly<
  Ref<Readonly<TemplateRefsList<T>>>
>
```
