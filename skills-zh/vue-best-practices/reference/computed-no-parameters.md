---
title: 计算属性不能接受参数
impact: MEDIUM
impactDescription: 尝试将参数传递给计算属性会失败或破坏缓存
type: capability
tags: [vue3, computed, methods, parameters, common-mistake]
---

# 计算属性不能接受参数

**影响：中** - 计算属性设计为在没有参数的情况下从响应式状态派生值。尝试传递参数会破坏缓存机制或导致错误。请改用方法或返回函数的计算属性。

## 任务清单

- [ ] 需要传递参数时使用方法
- [ ] 考虑参数是否可以是响应式状态
- [ ] 如果必须参数化，请理解返回函数会失去缓存好处
- [ ] 对于参数化操作，首选在模板中调用方法

**错误：**
```vue
<template>
  <!-- 错误：计算属性不能像这样接受参数 -->
  <p>{{ filteredItems('active') }}</p>
  <p>{{ formattedPrice(100, 'USD') }}</p>
</template>

<script setup>
import { ref, computed } from 'vue'

const items = ref([/* ... */])

// 错误：这不会按预期工作
// 计算属性被调用一次，而不是每个参数一次
const filteredItems = computed((status) => {  // status 将是 undefined 或之前的值
  return items.value.filter(i => i.status === status)
})
</script>
```

```vue
<script>
export default {
  data() {
    return { items: [/* ... */] }
  },
  computed: {
    // 错误：计算属性不接收参数
    filteredItems(status) {  // 'status' 实际上是 'this' 或 undefined
      return this.items.filter(i => i.status === status)
    }
  }
}
</script>
```

**正确：**
```vue
<template>
  <!-- 正确：对参数化操作使用方法 -->
  <p>{{ getFilteredItems('active') }}</p>
  <p>{{ formatPrice(100, 'USD') }}</p>

  <!-- 正确：或使用带有响应式过滤状态的计算属性 -->
  <select v-model="statusFilter">
    <option value="active">Active</option>
    <option value="inactive">Inactive</option>
  </select>
  <p>{{ filteredItems }}</p>
</template>

<script setup>
import { ref, computed } from 'vue'

const items = ref([/* ... */])
const statusFilter = ref('active')

// 正确：用于参数化操作的方法
function getFilteredItems(status) {
  return items.value.filter(i => i.status === status)
}

function formatPrice(amount, currency) {
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency
  }).format(amount)
}

// 正确：带有响应式参数的计算属性
const filteredItems = computed(() => {
  return items.value.filter(i => i.status === statusFilter.value)
})
</script>
```

## 变通方法：返回函数的计算属性

如果你需要带有参数的类似计算属性的功能，可以返回一个函数。**但是，这会破坏缓存好处：**

```vue
<template>
  <p>{{ getItemsByStatus('active') }}</p>
</template>

<script setup>
import { ref, computed } from 'vue'

const items = ref([/* ... */])

// 这可以工作，但不提供缓存好处
// 内部函数在每次调用时都会运行
const getItemsByStatus = computed(() => {
  return (status) => items.value.filter(i => i.status === status)
})

// 这本质上等同于仅使用方法
// 仅在你需要与其他计算属性组合时才有用
</script>
```

## 何时使用每种方法

| 场景 | 方法 | 缓存 |
|----------|----------|---------|
| 基于响应式状态的固定过滤器 | 计算属性 | 是 |
| 作为参数传递的动态过滤器 | 方法 | 否 |
| 来自用户选择的过滤器选项 | 计算属性 + 响应式参数 | 是 |
| 带有可变参数的格式化 | 方法 | 否 |
| 带有参数的组合派生 | 返回函数的计算属性 | 部分 |

## 使参数响应式

最好的模式通常是使"参数"成为响应式值：

```vue
<script setup>
import { ref, computed } from 'vue'

const items = ref([/* ... */])

// 与其将 'status' 作为参数传递：
const currentStatus = ref('active')

// 创建一个使用响应式状态的计算属性
const filteredItems = computed(() => {
  return items.value.filter(i => i.status === currentStatus.value)
})

// 通过更新 ref 来更改过滤器
function filterByStatus(status) {
  currentStatus.value = status
}
</script>
```

## 参考
- [Vue.js 计算属性](https://vuejs.org/guide/essentials/computed.html)
- [Vue.js 方法](https://vuejs.org/guide/essentials/reactivity-fundamentals.html#declaring-methods)
