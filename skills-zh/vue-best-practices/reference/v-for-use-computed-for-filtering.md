---
title: 使用计算属性进行列表过滤和排序
impact: MEDIUM
impactDescription: 计算属性缓存结果并仅在依赖项更改时重新计算 - 方法在每次渲染时重新计算
type: best-practice
tags: [vue3, v-for, computed, performance, list-rendering]
---

# 使用计算属性进行列表过滤和排序

**影响：中** - 当显示数组的过滤或排序版本时，始终首选计算属性而不是内联表达式或方法。计算属性根据其响应式依赖项进行缓存，并且仅在这些依赖项更改时重新评估。方法在每个渲染周期重新计算。

这对于大型列表或复杂的过滤/排序操作尤其重要，其中不必要的重新计算会影响性能。

## 任务清单

- [ ] 对过滤/排序列表显示使用计算属性
- [ ] 仅在需要传递参数时使用方法（例如，嵌套 v-for 循环）
- [ ] 将过滤/排序逻辑保留在 JavaScript 中，而不是在模板中
- [ ] 考虑大型列表的性能 - 计算缓存是你的朋友

**低效（每次渲染重新计算）：**
```html
<!-- 避免：内联过滤在每次渲染时重新计算 -->
<li v-for="item in items.filter(i => i.isActive)" :key="item.id">
  {{ item.name }}
</li>

<!-- 避免：方法调用在每次渲染时重新计算 -->
<li v-for="item in getActiveItems()" :key="item.id">
  {{ item.name }}
</li>
```

```javascript
// 方法在每次组件重新渲染时重新计算
function getActiveItems() {
  return items.value.filter(item => item.isActive)
}
```

**高效（缓存结果）：**
```javascript
const items = ref([
  { id:1, name: 'Item 1', isActive: true },
  { id:2, name: 'Item 2', isActive: false },
  { id:3, name: 'Item 3', isActive: true },
])

// 计算属性：仅在 items 更改时重新计算
const activeItems = computed(() => {
  return items.value.filter(item => item.isActive)
})

const sortedItems = computed(() => {
  return [...items.value].sort((a, b) => a.name.localeCompare(b.name))
})

// 组合过滤和排序
const activeSortedItems = computed(() => {
  return items.value
    .filter(item => item.isActive)
    .sort((a, b) => a.name.localeCompare(b.name))
})
```

```html
<!-- 正确：使用缓存的计算值 -->
<li v-for="item in activeItems" :key="item.id">
  {{ item.name }}
</li>
```

## 何时使用方法是合适的

在需要传递参数时使用方法，例如在嵌套 v-for 循环中：

```javascript
// 这里需要方法，因为我们需要将 'category' 作为参数传递
function getItemsByCategory(category) {
  return items.value.filter(item => item.category === category)
}
```

```html
<div v-for="category in categories" :key="category.id">
  <h3>{{ category.name }}</h3>
  <ul>
    <!-- 这里需要方法来传递 category 参数 -->
    <li v-for="item in getItemsByCategory(category)" :key="item.id">
      {{ item.name }}
    </li>
  </ul>
</div>
```

## 参考
- [Vue.js 列表渲染 - 显示过滤/排序结果](https://vuejs.org/guide/essentials/list.html#displaying-filtered-sorted-results)
- [Vue.js 计算属性](https://vuejs.org/guide/essentials/computed.html)
