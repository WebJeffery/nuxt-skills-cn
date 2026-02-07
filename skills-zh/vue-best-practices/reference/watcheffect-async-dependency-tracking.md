---
title: watchEffect 只在第一个 await 之前跟踪依赖项
impact: HIGH
impactDescription: 在 await 之后访问的依赖项不会被跟踪，导致监视器错过响应式更改
type: capability
tags: [vue3, watchEffect, watchers, async, await, dependency-tracking]
---

# watchEffect 只在第一个 await 之前跟踪依赖项

**影响：高** - `watchEffect` 自动跟踪响应式依赖项，但仅在同步执行期间。在第一个 `await` 语句之后访问的任何响应式属性都不会被跟踪，并且对它们的更改不会触发监视器。

对于异步操作，要么在 await 之前访问所有依赖项，要么使用带有显式依赖项的 `watch`。

## 任务清单

- [ ] 在 watchEffect 中的第一个 await 之前访问所有响应式依赖项
- [ ] 当需要异步跟踪时，使用带有显式源的 `watch`
- [ ] 在 await 之前将响应式值存储在局部变量中
- [ ] 注意 await 之后的依赖项对 Vue 来说是不可见的

**错误：**
```vue
<script setup>
import { ref, watchEffect } from 'vue'

const userId = ref(1)
const includeDetails = ref(true)
const userData = ref(null)

// 错误：includeDetails 在 await 之后被访问 - 未被跟踪！
watchEffect(async () => {
  const response = await fetch(`/api/users/${userId.value}`)
  const data = await response.json()

  // 此依赖项未被跟踪 - 更改不会触发重新运行
  if (includeDetails.value) {
    userData.value = { ...data, details: await fetchDetails(data.id) }
  } else {
    userData.value = data
  }
})

// 错误：await 之后有多个依赖项
watchEffect(async () => {
  await someAsyncSetup()

  // 这些都没有被跟踪！
  console.log(optionA.value)  // 未被跟踪
  console.log(optionB.value)  // 未被跟踪
  doSomething(optionC.value)  // 未被跟踪
})
</script>
```

**正确：**
```vue
<script setup>
import { ref, watchEffect, watch } from 'vue'

const userId = ref(1)
const includeDetails = ref(true)
const userData = ref(null)

// 正确：在 await 之前访问所有依赖项
watchEffect(async () => {
  // 同步捕获响应式值
  const id = userId.value
  const withDetails = includeDetails.value

  // 现在这些都被跟踪了
  const response = await fetch(`/api/users/${id}`)
  const data = await response.json()

  if (withDetails) {
    userData.value = { ...data, details: await fetchDetails(data.id) }
  } else {
    userData.value = data
  }
})

// 替代方案：使用带有显式依赖项的 watch
watch(
  [userId, includeDetails],
  async ([id, withDetails]) => {
    const response = await fetch(`/api/users/${id}`)
    const data = await response.json()

    if (withDetails) {
      userData.value = { ...data, details: await fetchDetails(data.id) }
    } else {
      userData.value = data
    }
  },
  { immediate: true }
)
</script>
```

## 模式：首先提取依赖项

```vue
<script setup>
import { ref, watchEffect } from 'vue'

const filters = ref({ status: 'active', sortBy: 'name' })
const page = ref(1)
const results = ref([])

// 正确：同步提取所有需要的值
watchEffect(async () => {
  // 在 await 之前访问所有依赖项 - 全部被跟踪！
  const { status, sortBy } = filters.value
  const currentPage = page.value

  // 现在可以安全地进行异步工作
  const response = await fetch(
    `/api/items?status=${status}&sort=${sortBy}&page=${currentPage}`
  )
  results.value = await response.json()
})
</script>
```

## 何时改用 watch

```vue
<script setup>
import { ref, watch } from 'vue'

const source = ref('initial')
const option = ref('default')
const result = ref(null)

// 对于复杂异步最佳：使用带有显式源的 watch
// 所有依赖项都被显式声明并始终被跟踪
watch(
  [source, option],
  async ([sourceVal, optionVal]) => {
    const data = await processAsync(sourceVal)
    result.value = applyOption(data, optionVal)
  },
  { immediate: true }
)
</script>
```

## 调试未跟踪的依赖项

```vue
<script setup>
import { ref, watchEffect } from 'vue'

const a = ref(1)
const b = ref(2)

watchEffect(async () => {
  console.log('Tracked dependency a:', a.value)  // 被跟踪

  await someAsyncOperation()

  console.log('Untracked dependency b:', b.value)  // 未被跟踪！
  // 更改 b.value 不会重新运行此 watchEffect
})

// 测试：更改 a.value -> watchEffect 重新运行
// 测试：更改 b.value -> watchEffect 不会重新运行
</script>
```

## 参考
- [Vue.js 监听器 - watchEffect](https://vuejs.org/guide/essentials/watchers.html#watcheffect)
- [Vue.js API - watchEffect](https://vuejs.org/api/reactivity-core.html#watcheffect)
