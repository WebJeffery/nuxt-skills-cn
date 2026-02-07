---
title: 混合命名插槽和作用域插槽时使用显式默认模板
impact: HIGH
impactDescription: 在组件上使用 v-slot 同时内部有命名插槽会导致作用域模糊和编译错误
type: gotcha
tags: [vue3, slots, scoped-slots, named-slots, compilation-error]
---

# 混合命名插槽和作用域插槽时使用显式默认模板

**影响：高** - 当组件同时使用默认作用域插槽和命名插槽时，必须为默认插槽使用显式的 `<template #default>`。在组件元素上使用 `v-slot` 同时内部有嵌套的命名插槽模板会导致作用域模糊和编译错误。

## 任务清单

- [ ] 当在具有属性的默认插槽旁边使用命名插槽时，始终使用显式的 `<template #default>`
- [ ] 永远不要混合在组件元素上使用 `v-slot` 和内部使用 `<template #name>`
- [ ] 保持插槽作用域清晰明确

**错误：**
```vue
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <!-- 错误：在组件上使用 v-slot + 内部命名模板会导致模糊 -->
  <MyComponent v-slot="{ message }">
    <p>{{ message }}</p>

    <template #footer>
      <!-- 模糊：'message' 在这里可用吗？Vue 无法确定 -->
      <p>页脚: {{ message }}</p>
    </template>
  </MyComponent>
</template>
```

这会导致编译错误，因为 Vue 无法确定：
1. 默认插槽的 `message` 是否应该在页脚插槽中可用
2. 哪个作用域适用于非模板内容

**正确：**
```vue
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <!-- 正确：每个插槽使用显式模板，作用域清晰 -->
  <MyComponent>
    <template #default="{ message }">
      <p>{{ message }}</p>
    </template>

    <template #footer>
      <!-- 清晰：页脚插槽有自己的作用域，无法访问默认插槽的 'message' -->
      <p>页脚内容在这里</p>
    </template>
  </MyComponent>
</template>
```

**正确 - 当页脚也有属性时：**
```vue
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <MyComponent>
    <template #default="{ message }">
      <p>{{ message }}</p>
    </template>

    <template #footer="{ year }">
      <!-- 每个插槽接收自己的属性 -->
      <p>版权所有 {{ year }}</p>
    </template>
  </MyComponent>
</template>
```

## 规则

当你有**任何**命名插槽（`<template #name>`）时，始终为**所有**插槽使用显式模板，包括默认插槽。这使得作用域边界清晰并防止编译错误。

| 模式 | 有效？ | 备注 |
|---------|--------|-------|
| 仅在组件上使用 `v-slot` | 是 | 单个默认作用域插槽 |
| 仅命名模板 | 是 | 多个命名插槽 |
| 在组件上使用 `v-slot` + 命名模板 | 否 | 作用域模糊 |
| 所有显式模板 | 是 | 每个插槽的作用域清晰 |

## 参考
- [Vue.js 插槽 - 命名作用域插槽](https://vuejs.org/guide/components/slots.html#named-scoped-slots)
