---
title: 根据依赖控制需求选择 watch vs watchEffect
impact: MEDIUM
impactDescription: 错误的选择会导致不必要的重新运行或错过依赖跟踪
type: efficiency
tags: [vue3, watch, watchEffect, watchers, reactivity, best-practices]
---

# 根据依赖控制需求选择 watch vs watchEffect

**影响：中** - 当 `watchEffect` 更简洁时使用 `watch` 会导致重复代码。当需要 `watch` 时使用 `watchEffect` 可能会导致意外的重新运行或错过依赖项（特别是在异步情况下）。

当回调使用与应该触发它的相同状态时，使用 `watchEffect` 处理简单情况。当你需要精确控制什么触发回调、访问旧值或延迟执行时，使用 `watch`。

## 任务清单

- [ ] 当回调逻辑使用与其应该反应的相同状态时使用 `watchEffect`
- [ ] 当需要旧值比较时使用 `watch`
- [ ] 当需要延迟执行（不立即）时使用 `watch`
- [ ] 对于 await 后有依赖项的异步回调使用 `watch`
- [ ] 当回调不应在初始挂载上运行时使用 `watch`

## 比较表

| 特性 | `watch` | `watchEffect` |
|---------|---------|---------------|
| 依赖跟踪 | 显式（你指定） | 自动（使用访问的属性） |
| 默认延迟 | 是（仅在更改时运行） | 否（立即运行） |
| 访问旧值 | 是 | 否 |
| 异步依赖跟踪 | 完全控制 | 仅在第一次 await 之前 |
| 多个源 | 数组语法 | 自动 |

**何时首选 `watchEffect`：**
```vue
<script setup>
import { ref, watchEffect } from 'vue'

const todoId = ref(1)
const data = ref(null)

// 正确：当回调使用相同状态时，watchEffect 更简洁
watchEffect(async () => {
  const response = await fetch(
    `https://api.example.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
</script>
```

**何时首选 `watch`：**
```vue
<script setup>
import { ref, watch } from 'vue'

const todoId = ref(1)
const data = ref(null)

// 使用 watch 更好，当：

// 1. 你需要旧值
watch(todoId, (newId, oldId) => {
  console.log(`从 ${oldId} 更改为 ${newId}`)
})

// 2. 你不希望立即执行
watch(todoId, () => {
  // 仅在 todoId 更改时运行，不在挂载时
  fetchData()
})

// 3. 你在 await 之后有依赖项
watch(todoId, async (id) => {
  const response = await fetch(`/api/todos/${id}`)
  // 这里的更多响应式访问仍然正确触发
  // 因为我们明确指定了 todoId 作为源
})
</script>
```

## 使用 watchEffect 避免重复代码

```vue
<script setup>
import { ref, watch, watchEffect } from 'vue'

const searchQuery = ref('')
const category = ref('all')
const results = ref([])

// 错误：重复 - 在源和回调中列出相同的依赖项
watch(
  [searchQuery, category],
  ([query, cat]) => {
    fetchResults(query, cat)  // 相同的变量重复
  }
)

// 正确：watchEffect 消除重复
watchEffect(() => {
  fetchResults(searchQuery.value, category.value)
})
</script>
```

## 使用 watch 进行延迟行为

```vue
<script setup>
import { ref, watch, watchEffect } from 'vue'

const userId = ref(null)

// 错误：即使 userId 为 null 也立即运行
watchEffect(() => {
  if (userId.value) {
    loadUserProfile(userId.value)
  }
})

// 正确：仅在 userId 实际更改时运行
watch(userId, (id) => {
  if (id) {
    loadUserProfile(id)
  }
})

// 也正确：当你需要两种行为时，使用带有 immediate 的 watch
watch(
  userId,
  (id) => {
    if (id) loadUserProfile(id)
  },
  { immediate: true }  // 明确表示立即运行
)
</script>
```

## 使用 watch 进行旧值比较

```vue
<script setup>
import { ref, watch } from 'vue'

const status = ref('pending')

// 只有 watch() 提供旧值
watch(status, (newStatus, oldStatus) => {
  if (oldStatus === 'pending' && newStatus === 'approved') {
    showApprovalNotification()
  }

  if (oldStatus === 'approved' && newStatus === 'rejected') {
    showRejectionWarning()
  }
})
</script>
```

## 使用 watch 进行复杂的异步依赖项

```vue
<script setup>
import { ref, watch } from 'vue'

const filters = ref({ status: 'active', sort: 'date' })
const page = ref(1)
const results = ref([])

// 更好：watch 对异步使用显式源
// 无论 await 位置如何，所有依赖项都被跟踪
watch(
  [filters, page],
  async ([currentFilters, currentPage]) => {
    const data = await fetchWithFilters(currentFilters)

    // 这些仍然被正确跟踪：
    results.value = paginateResults(data, currentPage)
  },
  { deep: true }
)
</script>
```

## 参考
- [Vue.js 监听器 - watch vs. watchEffect](https://vuejs.org/guide/essentials/watchers.html#watch-vs-watcheffect)
