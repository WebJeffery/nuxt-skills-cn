---
title: 将 Props 传递给 Composables 时保持响应性
impact: HIGH
impactDescription: 直接将 prop 值传递给 composables 会丢失响应性 - 当 props 更改时 composable 不会更新
type: gotcha
tags: [vue3, props, composables, reactivity, composition-api]
---

# 将 Props 传递给 Composables 时保持响应性

**影响：高** - 一个常见的错误是将从 prop 接收的数据直接传递给 composable。这传递的是当前值，而不是响应式源。当 prop 更新时，composable 不会收到新值，导致数据过时。

这是 Vue 3 中"我的 composable 不更新"错误的最常见来源之一。

## 任务清单

- [ ] 通过计算属性或 getter 函数将 props 传递给 composables
- [ ] 将多个 props 传递时使用 `toRefs()` 以保持响应性
- [ ] 在 composables 中，使用 `toValue()` 来规范化可能是 getter 或 refs 的输入
- [ ] 测试当 props 更改时 composable 输出是否更新

**错误：**
```vue
<script setup>
import { useFetch } from './composables/useFetch'
import { useDebounce } from './composables/useDebounce'

const props = defineProps({
  userId: Number,
  searchQuery: String
})

// 错误：传递初始值，而不是响应式源
// 当 userId 更改时 useFetch 永远不会重新获取！
const { data } = useFetch(`/api/users/${props.userId}`)

// 错误：防抖值在初始 searchQuery 处冻结
const debouncedQuery = useDebounce(props.searchQuery, 300)
</script>
```

**正确：**
```vue
<script setup>
import { computed } from 'vue'
import { useFetch } from './composables/useFetch'
import { useDebounce } from './composables/useDebounce'

const props = defineProps({
  userId: Number,
  searchQuery: String
})

// 正确：使用 computed 创建响应式 URL
const userUrl = computed(() => `/api/users/${props.userId}`)
const { data } = useFetch(userUrl)

// 正确：传递 getter 函数以保持响应性
const debouncedQuery = useDebounce(() => props.searchQuery, 300)
</script>
```

## 模式：使用 toRefs 处理多个 Props

```vue
<script setup>
import { toRefs } from 'vue'
import { useUserForm } from './composables/useUserForm'

const props = defineProps({
  initialName: String,
  initialEmail: String,
  initialAge: Number
})

// 将所有 props 转换为 refs，保持响应性
const { initialName, initialEmail, initialAge } = toRefs(props)

// 现在每个都是跟踪 prop 更改的 ref
const { form, isValid } = useUserForm({
  name: initialName,
  email: initialEmail,
  age: initialAge
})
</script>
```

## 编写响应式安全的 Composables

Composables 应该使用 `toValue()` 接受多种输入类型：

```javascript
// composables/useDebounce.js
import { ref, watch, toValue } from 'vue'

export function useDebounce(source, delay = 300) {
  // toValue() 处理：ref、getter 函数或普通值
  const debounced = ref(toValue(source))
  let timeout

  watch(
    () => toValue(source),  // 规范化任何输入类型
    (newValue) => {
      clearTimeout(timeout)
      timeout = setTimeout(() => {
        debounced.value = newValue
      }, delay)
    },
    { immediate: true }
  )

  return debounced
}
```

```javascript
// composables/useFetch.js
import { ref, watchEffect, toValue } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)
  const loading = ref(false)

  watchEffect(async () => {
    loading.value = true
    error.value = null

    try {
      // toValue() 使其适用于 computed、getter 或字符串
      const response = await fetch(toValue(url))
      data.value = await response.json()
    } catch (e) {
      error.value = e
    } finally {
      loading.value = false
    }
  })

  return { data, error, loading }
}
```

## 快速参考：输入类型

| Composable 的输入 | 响应式？ | 示例 |
|---------------------|-----------|---------|
| `props.value` | 否 | `useFetch(props.userId)` |
| `computed(() => ...)` | 是 | `useFetch(computed(() => props.userId))` |
| `() => props.value` | 是* | `useFetch(() => props.userId)` |
| `toRef(props, 'key')` | 是 | `useFetch(toRef(props, 'userId'))` |
| `toRefs(props).key` | 是 | `const { userId } = toRefs(props); useFetch(userId)` |

*需要 composable 内部使用 `toValue()`

## 参考
- [Vue.js Reactivity API - toValue](https://vuejs.org/api/reactivity-utilities.html#tovalue)
- [Vue.js Composables - Conventions and Best Practices](https://vuejs.org/guide/reusability/composables.html#conventions-and-best-practices)
