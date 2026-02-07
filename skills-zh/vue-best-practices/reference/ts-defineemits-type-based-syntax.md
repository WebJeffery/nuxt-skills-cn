---
title: 使用基于类型的 defineEmits 以获得更好的 TypeScript 支持
impact: MEDIUM
impactDescription: 基于类型的 emit 声明为事件负载提供细粒度的类型检查
type: best-practice
tags: [vue3, typescript, emits, defineEmits, composition-api, vue3.3]
---

# 使用基于类型的 defineEmits 以获得更好的 TypeScript 支持

**影响：中等** - Vue 3.3+ 引入了使用调用签名的更简洁的基于类型的 `defineEmits` 语法。这提供了更好的 TypeScript 集成，对事件负载有细粒度的控制。

## 任务清单

- [ ] 使用调用签名语法定义 defineEmits（Vue 3.3+）
- [ ] 定义所有事件名称及其负载类型
- [ ] 使用带标签的元组元素以获得更清晰的 API 文档
- [ ] 不要混合运行时和基于类型的声明

## Vue 3.3+ 语法（推荐）

```vue
<script setup lang="ts">
// 最简洁且类型安全的方法
const emit = defineEmits<{
  change: [id: number]
  update: [value: string]
  submit: [data: FormData, validated: boolean]
  close: []  // 无负载
}>()

// 使用 - 完全类型化
emit('change', 42)           // OK
emit('update', 'new value')  // OK
emit('submit', formData, true) // OK
emit('close')                // OK

emit('change', 'string')     // 错误：期望 number
emit('update')               // 错误：缺少参数
emit('unknown')              // 错误：不是声明的事件
</script>
```

## Vue 3.3 之前的语法

在 Vue 3.3 之前，使用函数签名语法：

```vue
<script setup lang="ts">
// 较旧的语法 - 仍然有效但更冗长
const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
  (e: 'close'): void
}>()
</script>
```

## 运行时声明（不推荐用于 TypeScript）

```vue
<script setup lang="ts">
// 运行时声明 - 类型安全性较低
const emit = defineEmits(['change', 'update', 'close'])

// emit('change', anyValue) - 负载没有类型检查！
// emit('typo')             - 未知事件没有错误
</script>
```

## 不能混合声明样式

```typescript
// 错误：不能组合运行时和基于类型
const emit = defineEmits<{ change: [id: number] }>(['change'])  // 错误！

// 正确：选择一种方法
const emit = defineEmits<{ change: [id: number] }>()  // 基于类型
// 或
const emit = defineEmits(['change'])  // 仅运行时
```

## 复杂负载类型

```vue
<script setup lang="ts">
interface User {
  id: string
  name: string
  email: string
}

interface FormState {
  isValid: boolean
  errors: string[]
}

const emit = defineEmits<{
  // 单个复杂对象
  'user-selected': [user: User]

  // 多个参数
  'form-validated': [state: FormState, data: Record<string, unknown>]

  // 可选负载（使用带可选元素的数组）
  'modal-closed': [reason?: string]

  // 联合类型
  'status-changed': [status: 'pending' | 'active' | 'completed']
}>()

// 使用
emit('user-selected', { id: '1', name: 'John', email: 'john@example.com' })
emit('form-validated', formState, formData)
emit('modal-closed')          // OK - reason 是可选的
emit('modal-closed', 'cancel') // 也 OK
emit('status-changed', 'active')
</script>
```

## 事件命名约定

```vue
<script setup lang="ts">
const emit = defineEmits<{
  // 事件名称使用 kebab-case（与 HTML 事件一致）
  'item-selected': [item: Item]
  'page-changed': [page: number]

  // 常见模式
  'update:modelValue': [value: string]  // 用于 v-model
  'update:visible': [visible: boolean]  // 用于命名 v-model
}>()
</script>

<template>
  <!-- 模板中的事件使用 kebab-case -->
  <ChildComponent
    @item-selected="handleItemSelected"
    @page-changed="handlePageChange"
  />
</template>
```

## 提取 Emit 类型以供重用

```typescript
// types/events.ts
export interface ListEvents<T> {
  'item-selected': [item: T]
  'item-deleted': [item: T, index: number]
  'selection-changed': [items: T[]]
}
```

```vue
<script setup lang="ts" generic="T">
import type { ListEvents } from '@/types/events'

const emit = defineEmits<ListEvents<T>>()
</script>
```

## 与 Props 结合以获得完整的组件类型

```vue
<script setup lang="ts">
interface Props {
  modelValue: string
  items: string[]
}

interface Emits {
  'update:modelValue': [value: string]
  'item-added': [item: string]
  'item-removed': [item: string, index: number]
}

const props = defineProps<Props>()
const emit = defineEmits<Emits>()

function addItem(item: string) {
  emit('item-added', item)
  emit('update:modelValue', props.modelValue + item)
}
</script>
```

## 参考
- [Vue.js TypeScript 与 Composition API - Emits](https://vuejs.org/guide/typescript/composition-api.html#typing-component-emits)
- [Vue.js Component Events](https://vuejs.org/guide/components/events.html)
