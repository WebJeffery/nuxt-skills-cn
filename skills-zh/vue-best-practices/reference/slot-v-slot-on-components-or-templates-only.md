---
title: v-slot 只能用于组件或模板标签
impact: HIGH
impactDescription: 在 HTML 元素上使用 v-slot 会导致编译错误
type: gotcha
tags: [vue3, slots, v-slot, compilation-error, common-mistake]
---

# v-slot 只能用于组件或模板标签

**影响：高** - `v-slot` 指令（及其简写 `#`）只能用于 Vue 组件或 `<template>` 标签。在原生 HTML 元素（如 `<div>` 或 `<span>`）上使用它会导致 Vue 编译错误。

## 任务清单

- [ ] 仅在组件元素或 `<template>` 标签上使用 `v-slot`
- [ ] 使用默认作用域插槽简写时，应用于组件本身
- [ ] 对于命名插槽，始终使用 `<template #name>` 语法

**错误：**
```vue
<template>
  <!-- 错误：在原生 HTML 元素上使用 v-slot -->
  <div v-slot:header>
    <h1>标题</h1>
  </div>

  <!-- 错误：在 HTML 元素上使用简写 -->
  <span #default="{ item }">
    {{ item.name }}
  </span>

  <!-- 错误：在纯 HTML 元素内部使用 v-slot -->
  <div>
    <p v-slot:content>一些文本</p>
  </div>
</template>
```

这些会导致错误：`v-slot can only be used on components or <template> tags`

**正确：**
```vue
<template>
  <!-- 正确：在组件元素上使用 v-slot（默认作用域插槽） -->
  <MyComponent v-slot="{ item }">
    {{ item.name }}
  </MyComponent>

  <!-- 正确：命名插槽使用模板标签 -->
  <BaseLayout>
    <template #header>
      <h1>标题</h1>
    </template>

    <template #default>
      <p>主要内容</p>
    </template>

    <template #footer>
      <p>页脚内容</p>
    </template>
  </BaseLayout>

  <!-- 正确：在组件上使用简写作为默认插槽 -->
  <FancyList #default="{ item }">
    <div>{{ item.name }}</div>
  </FancyList>
</template>
```

## 常见场景

### 在 HTML 中包装插槽内容

如果你需要在插槽内容周围有 HTML 包装器，请将它们放在模板内部：

```vue
<!-- 错误 -->
<MyComponent>
  <div v-slot:header class="header-wrapper">
    <h1>标题</h1>
  </div>
</MyComponent>

<!-- 正确 -->
<MyComponent>
  <template #header>
    <div class="header-wrapper">
      <h1>标题</h1>
    </div>
  </template>
</MyComponent>
```

### 同一元素上的多个 v-slot

当你在同一元素上有多个 v-slot 指令时会发生另一个错误 - 只有第一个被识别：

```vue
<!-- 错误：多个 v-slot 指令 -->
<MyComponent v-slot:header v-slot:footer>
  内容
</MyComponent>

<!-- 正确：每个插槽使用单独的模板 -->
<MyComponent>
  <template #header>页眉</template>
  <template #footer>页脚</template>
</MyComponent>
```

## 有效的 v-slot 位置

| 元素类型 | 允许 v-slot？ | 示例 |
|--------------|-----------------|---------|
| 组件 | 是 | `<MyComponent v-slot="props">` |
| `<template>` | 是 | `<template #header>` |
| `<div>` | 否 | 编译错误 |
| `<span>` | 否 | 编译错误 |
| 任何 HTML 元素 | 否 | 编译错误 |

## 参考
- [Vue.js 插槽](https://vuejs.org/guide/components/slots.html)
- [DeepScan - vue-misused-v-slot](https://deepscan.io/docs/rules/vue-misused-v-slot)
