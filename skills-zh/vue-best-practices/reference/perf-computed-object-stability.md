---
title: 从计算属性返回稳定的对象引用
impact: MEDIUM
impactDescription: 返回新对象的计算属性即使在值没有有意义地更改时也会触发效果
type: efficiency
tags: [vue3, computed, performance, reactivity, vue3.4]
---

# 从计算属性返回稳定的对象引用

**影响: 中等** - 在 Vue 3.4+ 中，计算属性仅在其值更改时触发效果。但是，如果计算每次都返回一个新对象，Vue 无法检测到内部值是相同的。这会导致不必要的效果重新运行。

对于原始值，Vue 3.4+ 会自动处理此问题。对于对象，手动比较并在没有发生有意义更改时返回先前的值。

## 任务清单

- [ ] 对于返回原始值的计算属性，Vue 3.4+ 自动处理稳定性
- [ ] 对于返回对象的计算属性，与先前的值比较，如果未更改则返回旧引用
- [ ] 始终在比较之前执行完整计算（以正确跟踪依赖项）
- [ ] 考虑你是否真的需要返回对象，或者原始值是否足够

**错误：**
```vue
<script setup>
import { ref, computed, watchEffect } from 'vue'

const count = ref(0)

// 错误：每次都返回新对象，总是触发效果
const stats = computed(() => {
  return {
    isEven: count.value % 2 === 0,
    doubleValue: count.value * 2
  }
})

watchEffect(() => {
  console.log('统计已更改：', stats.value)
  // 在每次计数更改时记录，即使 isEven 没有更改
  // count: 0 -> 2 -> 4: isEven 始终为 true，但效果每次都运行
})
</script>
```

**正确：**
```vue
<script setup>
import { ref, computed, watchEffect } from 'vue'

const count = ref(0)

// 正确（Vue 3.4+）：原始计算 - 自动稳定性
const isEven = computed(() => count.value % 2 === 0)

watchEffect(() => {
  console.log('isEven：', isEven.value)
  // 仅在 isEven 实际更改时记录（0、2、4 不会重新触发）
})

// 正确（Vue 3.4+）：对象返回的手动比较
const stats = computed((oldValue) => {
  // 步骤 1：始终先计算新值（以跟踪依赖项）
  const newValue = {
    isEven: count.value % 2 === 0,
    category: count.value < 10 ? 'small' : 'large'
  }

  // 步骤 2：与先前的值比较
  if (oldValue &&
      oldValue.isEven === newValue.isEven &&
      oldValue.category === newValue.category) {
    return oldValue  // 返回旧引用 - 没有效果触发
  }

  return newValue
})

watchEffect(() => {
  console.log('统计已更改：', stats.value)
  // 现在仅在 isEven 或 category 实际更改时记录
})
</script>
```

## 原始值 vs 对象计算行为（Vue 3.4+）

```javascript
import { ref, computed, watchEffect } from 'vue'

const count = ref(0)

// 原始值：Vue 自动检测值没有更改
const isEven = computed(() => count.value % 2 === 0)

watchEffect(() => console.log(isEven.value))  // true

count.value = 2  // isEven 仍然为 true - 没有日志
count.value = 4  // isEven 仍然为 true - 没有日志
count.value = 3  // isEven 现在为 false - 记录：false

// 对象：每次都有新引用（没有手动比较）
const obj = computed(() => ({ isEven: count.value % 2 === 0 }))

watchEffect(() => console.log(obj.value))  // { isEven: true }

count.value = 2  // 再次记录！新对象引用
count.value = 4  // 再次记录！新对象引用
```

## 高级：深度对象比较

```javascript
import { ref, computed } from 'vue'
import { isEqual } from 'lodash-es'  // 用于深度比较

const filters = ref({ category: 'all', sortBy: 'date', page: 1 })

// 对于复杂对象，使用深度比较
const activeFilters = computed((oldValue) => {
  const newValue = {
    ...filters.value,
    hasFilters: filters.value.category !== 'all' || filters.value.sortBy !== 'date'
  }

  // 复杂对象的深度比较
  if (oldValue && isEqual(oldValue, newValue)) {
    return oldValue
  }

  return newValue
})
```

## 重要：始终在比较之前计算

```javascript
// 错误：早期返回阻止依赖项跟踪
const optimized = computed((oldValue) => {
  if (oldValue && someCondition) {
    return oldValue  // 依赖项未跟踪！
  }
  return computeExpensiveValue()
})

// 正确：先计算，然后比较
const optimized = computed((oldValue) => {
  const newValue = computeExpensiveValue()  // 始终跟踪依赖项
  if (oldValue && newValue === oldValue) {
    return oldValue
  }
  return newValue
})
```

## 参考
- [Vue.js 性能 - 计算稳定性](https://vuejs.org/guide/best-practices/performance.html#computed-stability)
- [Vue.js 计算属性](https://vuejs.org/guide/essentials/computed.html)
