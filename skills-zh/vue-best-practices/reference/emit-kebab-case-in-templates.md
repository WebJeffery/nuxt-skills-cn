---
title: 在模板中使用 kebab-case 作为事件监听器
impact: LOW
impactDescription: Vue 自动将 camelCase 发出转换为 kebab-case 监听器，但一致性提高了可读性
type: best-practice
tags: [vue3, events, emit, naming-convention, templates]
---

# 在模板中使用 kebab-case 作为事件监听器

**影响: 低** - Vue 自动在 camelCase 和 kebab-case 之间转换事件名称。您可以使用 camelCase 发出事件（`emit('someEvent')`）并使用 kebab-case 监听（`@some-event`）。然而，遵循一致的约定可以提高代码可读性并匹配 HTML 属性约定。

## 任务清单

- [ ] 在 JavaScript 中使用 camelCase 发出事件: `emit('updateValue')`
- [ ] 在模板中使用 kebab-case 监听事件: `@update-value`
- [ ] 在整个代码库中保持一致
- [ ] 理解 Vue 的自动大小写转换

## 约定

**推荐模式:**
```vue
<!-- ChildComponent.vue -->
<script setup>
const emit = defineEmits(['updateValue', 'itemSelected', 'formSubmit'])

function handleChange(value) {
  // 使用 camelCase 发出（JavaScript 约定）
  emit('updateValue', value)
}

function selectItem(item) {
  emit('itemSelected', item)
}
</script>
```

```vue
<!-- ParentComponent.vue -->
<template>
  <!-- 使用 kebab-case 监听（HTML 属性约定） -->
  <ChildComponent
    @update-value="handleUpdate"
    @item-selected="handleSelect"
    @form-submit="handleSubmit"
  />
</template>
```

## Vue 的自动转换

Vue 在**仅在模板语法中**自动处理这些转换：

| 发出 (camelCase) | 监听器 (kebab-case) | 有效？ |
|---------------------|----------------------|--------|
| `emit('updateValue')` | `@update-value` | 是 |
| `emit('itemSelected')` | `@item-selected` | 是 |
| `emit('formSubmit')` | `@form-submit` | 是 |

```vue
<!-- 在 Vue 3 模板中，所有这些等效工作 -->
<Child @updateValue="handler" />  <!-- camelCase 监听器 -->
<Child @update-value="handler" /> <!-- kebab-case 监听器（推荐） -->
```

### 重要: 仅限模板行为

这种自动转换**仅在模板语法中**有效（`@event-name`）。它在渲染函数或程序化事件监听器中**不**起作用：

```ts
// 在渲染函数中，使用带 'on' 前缀的 camelCase
import { h } from 'vue'

// 正确 - 带有 'on' 前缀的 camelCase 事件名称
h(ChildComponent, {
  onUpdateValue: (value) => handleUpdate(value),
  onItemSelected: (item) => handleSelect(item)
})

// 错误 - kebab-case 在渲染函数中不起作用
h(ChildComponent, {
  'onUpdate-value': (value) => handleUpdate(value),  // 不起作用！
  'on-update-value': (value) => handleUpdate(value)  // 不起作用！
})
```

```ts
// 程序化监听器也需要 camelCase
import { ref, onMounted } from 'vue'

const childRef = ref<ComponentPublicInstance | null>(null)

onMounted(() => {
  // 正确 - camelCase
  childRef.value?.$on?.('updateValue', handler)

  // 错误 - kebab-case 不会匹配
  childRef.value?.$on?.('update-value', handler)  // 不起作用！
})
```

**总结:**
- **模板**: 自动转换有效（`@update-value` 匹配 `emit('updateValue')`）
- **渲染函数**: 必须使用 `onEventName` 格式（带 `on` 前缀的 camelCase）
- **程序化监听器**: 必须使用精确的发出事件名称（通常是 camelCase）

## 为什么在模板中使用 kebab-case？

1. **HTML 约定**: HTML 属性不区分大小写，传统上使用 kebab-case
2. **与 props 的一致性**: Props 遵循相同的模式（`props.userName` -> `user-name="..."`）
3. **可读性**: `@user-profile-updated` 比 `@userProfileUpdated` 更易读
4. **Vue 风格指南**: Vue 的官方风格指南推荐此模式

## TypeScript 声明

使用 TypeScript 时，在 camelCase 中定义发出：

```vue
<script setup lang="ts">
const emit = defineEmits<{
  updateValue: [value: string]         // camelCase
  itemSelected: [item: Item]           // camelCase
  'update:modelValue': [value: string] // 特殊 v-model 语法（带冒号）
}>()
</script>
```

## v-model 事件

对于 v-model，`update:` 前缀使用冒号，而不是 kebab-case：

```vue
<script setup>
// 正确: v-model 更新使用冒号分隔符
const emit = defineEmits(['update:modelValue', 'update:firstName'])

function updateValue(newValue) {
  emit('update:modelValue', newValue)
}
</script>
```

```vue
<!-- 父组件 - v-model 自动处理事件 -->
<CustomInput v-model="value" />
<CustomInput v-model:first-name="firstName" />
```

## Vue 2 差异

在 Vue 2 中，事件名称没有自动大小写转换。这导致了问题：

```js
// Vue 2 - camelCase 事件无法在模板中监听
this.$emit('updateValue') // 作为 'updateValue' 发出

// 模板转换为小写
<child @updatevalue="handler">  // 作为 'updatevalue' 监听 - 不匹配！
```

Vue 3 通过自动 camelCase 到 kebab-case 转换修复了这个问题。

## 参考
- [Vue.js 组件事件](https://vuejs.org/guide/components/events.html)
- [Vue.js 风格指南 - 事件名称](https://vuejs.org/style-guide/)
