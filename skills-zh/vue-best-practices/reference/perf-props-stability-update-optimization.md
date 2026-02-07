---
title: 保持 Props 稳定以最小化子组件重新渲染
impact: HIGH
impactDescription: 将更改的 props 传递给列表项会导致所有子组件不必要地重新渲染
type: efficiency
tags: [vue3, performance, props, v-for, re-renders, optimization]
---

# 保持 Props 稳定以最小化子组件重新渲染

**影响: 高** - 当传递给子组件的 props 更改时，Vue 必须重新渲染这些组件。将派生值（如 `activeId`）传递给每个列表项会导致当 activeId 更改时所有项目都重新渲染，即使只有一个项目的活动状态实际发生了更改。

将比较逻辑移到父组件并传递布尔值结果。这是 Vue 中最有影响力的更新性能优化之一。

## 任务清单

- [ ] 避免传递所有子组件都与之比较的父级状态（如 `activeId`）
- [ ] 在父组件中预计算派生的布尔 props（如 `:active="item.id === activeId"`）
- [ ] 使用 Vue DevTools 分析重新渲染以识别 props 稳定性问题
- [ ] 对于大型列表，特别考虑此模式

**错误：**
```vue
<template>
  <!-- 错误：activeId 更改 -> 所有 100 个 ListItems 都重新渲染 -->
  <ListItem
    v-for="item in list"
    :key="item.id"
    :id="item.id"
    :active-id="activeId"
  />
</template>

<script setup>
import { ref } from 'vue'

const list = ref([/* 100 个项目 */])
const activeId = ref(null)

// 当 activeId 从 1 更改为 2 时：
// - ListItem 1 需要重新渲染（之前是活动的，现在不是）
// - ListItem 2 需要重新渲染（之前不是活动的，现在是活动的）
// - 所有其他 98 个 ListItems 也重新渲染，因为 activeId prop 更改了！
</script>
```

```vue
<!-- ListItem.vue - 接收 activeId 并在内部比较 -->
<template>
  <div :class="{ active: id === activeId }">
    {{ id }}
  </div>
</template>

<script setup>
defineProps({
  id: Number,
  activeId: Number  // 此 prop 对所有项目都更改
})
</script>
```

**正确：**
```vue
<template>
  <!-- 正确：只有 :active 实际更改的项目才会重新渲染 -->
  <ListItem
    v-for="item in list"
    :key="item.id"
    :id="item.id"
    :active="item.id === activeId"
  />
</template>

<script setup>
import { ref } from 'vue'

const list = ref([/* 100 个项目 */])
const activeId = ref(null)

// 当 activeId 从 1 更改为 2 时：
// - ListItem 1：:active 从 true 更改为 false -> 重新渲染
// - ListItem 2：:active 从 false 更改为 true -> 重新渲染
// - 所有其他 98 个 ListItems：:active 仍然是 false -> 不重新渲染！
</script>
```

```vue
<!-- ListItem.vue - 接收预计算的布尔值 -->
<template>
  <div :class="{ active }">
    {{ id }}
  </div>
</template>

<script setup>
defineProps({
  id: Number,
  active: Boolean  // 这仅对真正更改的项目更改
})
</script>
```

## 导致 Props 不稳定的常见模式

```vue
<!-- 错误：传递可能移动的索引 -->
<Item
  v-for="(item, index) in items"
  :key="item.id"
  :index="index"
  :total="items.length"  <!-- 当列表更改时更改 -->
/>

<!-- 错误：传递整个选择集 -->
<Item
  v-for="item in items"
  :key="item.id"
  :selected-ids="selectedIds"  <!-- 任何选择时所有项目都重新渲染 -->
/>

<!-- 正确：预计算布尔值 -->
<Item
  v-for="item in items"
  :key="item.id"
  :selected="selectedIds.includes(item.id)"
/>
```

## 性能影响示例

| 场景 | Props 更改 | 组件重新渲染 |
|----------|---------------|------------------------|
| 100 个项目，传递 `activeId` | 100 | 100（全部）|
| 100 个项目，传递 `:active` 布尔值 | 2 | 2（仅更改的）|
| 1000 个项目，传递 `activeId` | 1000 | 1000（全部）|
| 1000 个项目，传递 `:active` 布尔值 | 2 | 2（仅更改的）|

## 参考
- [Vue.js 性能 - Props 稳定性](https://vuejs.org/guide/best-practices/performance.html#props-stability)
