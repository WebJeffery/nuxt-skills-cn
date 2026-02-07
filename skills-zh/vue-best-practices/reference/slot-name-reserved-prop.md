---
title: 插槽名称是保留的，不包含在插槽属性中
impact: LOW
impactDescription: 期望在作用域插槽属性中有 'name' 但它是保留的会导致混淆
type: gotcha
tags: [vue3, slots, scoped-slots, reserved-props, naming]
---

# 插槽名称是保留的，不包含在插槽属性中

**影响：低** - 使用作用域插槽时，`<slot>` 元素上的 `name` 属性保留用于标识插槽。它不会作为插槽属性的一部分传递给父组件。

## 任务清单

- [ ] 不要期望插槽属性中有 `name` - 它是保留的
- [ ] 如果需要传递名称值，请使用不同的属性名称
- [ ] 记住只有显式绑定的属性才会成为插槽属性

**错误期望：**
```vue
<!-- ChildComponent.vue -->
<template>
  <div>
    <slot name="header" title="欢迎"></slot>
  </div>
</template>
```

```vue
<!-- ParentComponent.vue -->
<ChildComponent>
  <template #header="props">
    <!-- props = { title: "欢迎" } -->
    <!-- 'name' 不包含在内！ -->
    {{ props.name }}  <!-- 未定义 -->
    {{ props.title }} <!-- "欢迎" -->
  </template>
</ChildComponent>
```

**如果需要传递"名称"值：**
```vue
<!-- ChildComponent.vue -->
<template>
  <div>
    <!-- 使用不同的属性名称，如 'slotName' 或 'label' -->
    <slot name="header" :label="slotLabel" :title="title"></slot>
  </div>
</template>

<script setup>
const slotLabel = 'header'
const title = '欢迎'
</script>
```

```vue
<!-- ParentComponent.vue -->
<ChildComponent>
  <template #header="{ label, title }">
    <h2>{{ title }}</h2>
    <span>部分: {{ label }}</span>
  </template>
</ChildComponent>
```

## 什么作为插槽属性传递

| `<slot>` 上的属性 | 传递给父组件？ |
|----------------------|-------------------|
| `name` | 否（保留用于插槽标识） |
| `:text="message"` | 是，作为 `text` |
| `:count="5"` | 是，作为 `count` |
| `v-bind="object"` | 是，展开对象属性 |
| `class="..."` | 否（未用 `:` 绑定） |

## 多个命名插槽示例

```vue
<!-- TabPanel.vue -->
<template>
  <div class="tabs">
    <slot name="tab1" :active="activeTab === 1" :label="'第一个标签'"></slot>
    <slot name="tab2" :active="activeTab === 2" :label="'第二个标签'"></slot>
  </div>
</template>

<script setup>
import { ref } from 'vue'
const activeTab = ref(1)
</script>
```

```vue
<!-- 使用 -->
<TabPanel>
  <template #tab1="{ active, label }">
    <!-- 'name' 不可用，但 'label' 可用 -->
    <button :class="{ active }">{{ label }}</button>
  </template>

  <template #tab2="{ active, label }">
    <button :class="{ active }">{{ label }}</button>
  </template>
</TabPanel>
```

## 参考
- [Vue.js 插槽 - 作用域插槽](https://vuejs.org/guide/components/slots.html#scoped-slots)
