---
title: 将可变默认值包装在工厂函数中
impact: HIGH
impactDescription: 没有工厂函数，所有组件实例共享相同的可变引用，导致交叉污染错误
type: gotcha
tags: [vue3, typescript, props, withDefaults, mutable-types]
---

# 将可变默认值包装在工厂函数中

**影响：高** - 当使用 `withDefaults()` 与基于类型的 props 声明时，可变类型（数组和对象）的默认值必须包装在工厂函数中。没有这个，所有组件实例共享相同的引用，导致在一个实例中修改 prop 会影响所有其他实例的错误。

## 任务清单

- [ ] 始终将数组默认值包装在工厂函数中：`() => []`
- [ ] 始终将对象默认值包装在工厂函数中：`() => ({})`
- [ ] 原始类型（string、number、boolean）不需要工厂函数
- [ ] 检查现有组件的此模式

## 问题：共享的可变引用

**错误 - 在实例之间共享引用：**
```vue
<script setup lang="ts">
interface Props {
  items?: string[]
  config?: { theme: string }
}

const props = withDefaults(defineProps<Props>(), {
  items: ['default'],           // 错误！所有实例共享此数组
  config: { theme: 'light' }    // 错误！所有实例共享此对象
})
</script>
```

当您有此组件的多个实例时：
```vue
<template>
  <!-- 两者共享相同的 items 数组！-->
  <MyComponent ref="comp1" />
  <MyComponent ref="comp2" />
</template>

<script setup>
// 如果 comp1 修改其 items，comp2 的 items 也会更改！
comp1.value.items.push('new item')  // comp2 现在也有 'new item'
</script>
```

## 解决方案：工厂函数

**正确 - 每个组件的唯一实例：**
```vue
<script setup lang="ts">
interface Props {
  items?: string[]
  config?: { theme: string }
  nested?: { data: { values: number[] } }
}

const props = withDefaults(defineProps<Props>(), {
  items: () => ['default'],                      // 工厂函数！
  config: () => ({ theme: 'light' }),            // 工厂函数！
  nested: () => ({ data: { values: [] } })       // 工厂函数！
})
</script>
```

## 何时需要工厂函数

| 类型 | 需要工厂 | 示例默认值 |
|------|-----------------|-----------------|
| `string` | 否 | `'hello'` |
| `number` | 否 | `42` |
| `boolean` | 否 | `false` |
| `string[]` | **是** | `() => []` |
| `number[]` | **是** | `() => [1, 2, 3]` |
| `object` | **是** | `() => ({})` |
| `Map` | **是** | `() => new Map()` |
| `Set` | **是** | `() => new Set()` |
| `Date` | **是** | `() => new Date()` |

## 完整示例

```vue
<script setup lang="ts">
interface User {
  id: string
  name: string
}

interface Props {
  // 原始类型 - 不需要工厂
  title?: string
  count?: number
  disabled?: boolean

  // 可变类型 - 需要工厂
  items?: string[]
  users?: User[]
  metadata?: Record<string, unknown>
  selectedIds?: Set<string>
}

const props = withDefaults(defineProps<Props>(), {
  // 原始类型
  title: 'Default Title',
  count: 0,
  disabled: false,

  // 带有工厂函数的可变类型
  items: () => [],
  users: () => [],
  metadata: () => ({}),
  selectedIds: () => new Set()
})
</script>
```

## Vue 3.5+ 响应式 Props 解构

Vue 3.5 引入了响应式 props 解构，它自动处理这个问题：

```vue
<script setup lang="ts">
interface Props {
  items?: string[]
  config?: { theme: string }
}

// Vue 3.5+ - 默认值在没有显式工厂函数的情况下正常工作
const {
  items = ['default'],        // 每个实例获得自己的数组
  config = { theme: 'light' } // 每个实例获得自己的对象
} = defineProps<Props>()
</script>
```

注意：在底层，Vue 3.5 为您处理实例隔离。

## 常见错误模式

这个错误经常出现在列表/表格组件中：

```vue
<!-- ListItem.vue - 有错误 -->
<script setup lang="ts">
interface Props {
  selectedRows?: number[]
}

// 所有 ListItem 共享相同的 selectedRows 数组！
const props = withDefaults(defineProps<Props>(), {
  selectedRows: []  // 错误：缺少工厂函数
})
</script>
```

用户报告："在一个表中选择一行会在所有表中选择它！"

**修复：**
```typescript
const props = withDefaults(defineProps<Props>(), {
  selectedRows: () => []  // 现在每个实例都有自己的数组
})
```

## 参考
- [Vue.js TypeScript 与 Composition API - 默认 Props](https://vuejs.org/guide/typescript/composition-api.html#props-default-values)
- [Vue RFC - 响应式 Props 解构](https://github.com/vuejs/rfcs/discussions/502)
