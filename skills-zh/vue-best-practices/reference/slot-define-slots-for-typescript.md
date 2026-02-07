---
title: 使用 defineSlots 实现 TypeScript 类型安全
impact: MEDIUM
impactDescription: 没有 defineSlots，作用域插槽属性缺乏类型推断，可能导致运行时错误
type: best-practice
tags: [vue3, slots, typescript, type-safety, defineSlots, vue3.3]
---

# 使用 defineSlots 实现 TypeScript 类型安全

**影响：中等** - Vue 3.3+ 引入了 `defineSlots` 宏来添加插槽的类型定义。没有它，作用域插槽属性的类型为 `any`，失去了 TypeScript 的好处，并且由于错误的属性使用而存在运行时错误的风险。

## 任务清单

- [ ] 在暴露作用域插槽的 TypeScript 组件中使用 `defineSlots`
- [ ] 定义每个插槽接收的属性
- [ ] 返回类型当前被忽略，但按照约定使用 `any`
- [ ] 消费者可以获得插槽属性的自动完成和类型检查

**没有 defineSlots（没有类型安全）：**
```vue
<!-- FancyList.vue -->
<script setup lang="ts">
interface Item {
  id: number
  name: string
  price: number
}

const props = defineProps<{
  items: Item[]
}>()
</script>

<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      <!-- 插槽属性对消费者来说是 'any' 类型 -->
      <slot :item="item" :index="index" />
    </li>
  </ul>
</template>
```

```vue
<!-- Consumer.vue - 没有类型检查！ -->
<FancyList :items="products">
  <!-- 'item' 是 'any'，像 'item.nmae' 这样的拼写错误不会被捕获 -->
  <template #default="{ item }">
    {{ item.nmae }} <!-- 拼写错误不会被 TypeScript 捕获 -->
  </template>
</FancyList>
```

**有 defineSlots（完全类型安全）：**
```vue
<!-- FancyList.vue -->
<script setup lang="ts">
interface Item {
  id: number
  name: string
  price: number
}

const props = defineProps<{
  items: Item[]
}>()

// 定义插槽类型
defineSlots<{
  default(props: { item: Item; index: number }): any
  header(props: { count: number }): any
  empty(): any  // 没有属性的插槽
}>()
</script>

<template>
  <div>
    <header v-if="$slots.header">
      <slot name="header" :count="items.length" />
    </header>

    <ul v-if="items.length">
      <li v-for="(item, index) in items" :key="item.id">
        <slot :item="item" :index="index" />
      </li>
    </ul>

    <div v-else>
      <slot name="empty" />
    </div>
  </div>
</template>
```

```vue
<!-- Consumer.vue - 完全类型检查！ -->
<script setup lang="ts">
import FancyList from './FancyList.vue'

const products = ref([
  { id: 1, name: '小部件', price: 9.99 }
])
</script>

<template>
  <FancyList :items="products">
    <template #header="{ count }">
      <!-- 'count' 类型为 number -->
      <h2>{{ count }} 个项目</h2>
    </template>

    <template #default="{ item, index }">
      <!-- 'item' 类型为 Item，'index' 类型为 number -->
      <!-- TypeScript 捕获：item.nmae（拼写错误） -->
      <span>{{ index + 1 }}. {{ item.name }} - ${{ item.price }}</span>
    </template>

    <template #empty>
      <p>未找到项目</p>
    </template>
  </FancyList>
</template>
```

## 泛型插槽

对于具有泛型项目类型的组件：

```vue
<script setup lang="ts" generic="T">
defineProps<{
  items: T[]
}>()

defineSlots<{
  default(props: { item: T; index: number }): any
}>()
</script>
```

## 命名冲突警告

避免与父组件数据匹配的插槽属性名称：

```vue
<!-- 如果父组件作用域中有 'item'，插槽属性 'item' 可能会导致混淆 -->
<FancyList :items="products">
  <template #default="{ item }">
    <!-- 这里的 'item' 是插槽属性，不是父组件的 'item' -->
    {{ item.name }}
  </template>
</FancyList>
```

当可能发生冲突时，考虑重命名插槽属性以更具体：
```typescript
defineSlots<{
  default(props: { listItem: T; itemIndex: number }): any
}>()
```

## 要求

- Vue 3.3+ 用于 `defineSlots` 宏
- TypeScript 项目设置
- Volar 扩展用于 IDE 支持

## 参考
- [Vue.js 插槽 - TypeScript](https://vuejs.org/guide/components/slots.html#slots-with-typescript)
- [Escuela Vue - 使用 defineSlots 为插槽添加类型](https://escuelavue.es/en/devtips/typescript-vue-scoped-slots-defineslots)
