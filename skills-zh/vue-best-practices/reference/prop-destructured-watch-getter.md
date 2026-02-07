---
title: 将解构的 Props 包装在 Getter 中用于 watch 和 Composables
impact: MEDIUM
impactDescription: 直接将解构的 prop 值传递给 watch 或 composables 会丢失响应性
type: gotcha
tags: [vue3, props, reactivity, watch, composables, destructuring]
---

# 将解构的 Props 包装在 Getter 中用于 watch 和 Composables

**影响：中** - 当你使用 `defineProps` 解构 props 时，解构的变量在模板中是响应式的，但将它们直接传递给 `watch()` 或外部 composables 会传递当前值，而不是响应式源。watcher 或 composable 不会跟踪未来的更改。

Vue 3.5+ 自动转换解构的 props 以实现模板响应性，但外部函数仍然需要 getter 包装器。

## 任务清单

- [ ] 将解构的 props 包装在箭头函数中传递给 `watch()`
- [ ] 将解构的 props 传递给 composables 时使用 getter 函数
- [ ] 验证 composables 使用 `toValue()` 来规范化 getter/ref 输入
- [ ] 如果 getter 语法感觉尴尬，考虑直接使用 `props.propertyName`

**错误：**
```vue
<script setup>
import { watch } from 'vue'
import { useDebounce } from './composables'

const { searchQuery, userId } = defineProps(['searchQuery', 'userId'])

// 错误：传递值，而不是响应式源
// 这仅捕获初始值 - 更改不会触发 watcher
watch(searchQuery, (newValue) => {
  console.log('查询已更改：', newValue)  // 初始后永远不会触发！
})

// 错误：Composable 接收静态值
const debouncedQuery = useDebounce(searchQuery, 300)  // 不会更新
</script>
```

**正确：**
```vue
<script setup>
import { watch } from 'vue'
import { useDebounce } from './composables'

const { searchQuery, userId } = defineProps(['searchQuery', 'userId'])

// 正确：包装在 getter 函数中以保持响应性
watch(() => searchQuery, (newValue) => {
  console.log('查询已更改：', newValue)  // 每次更改时触发
})

// 正确：将 getter 传递给 composable
const debouncedQuery = useDebounce(() => searchQuery, 300)
</script>
```

## 替代方案：直接使用 props 对象

```vue
<script setup>
import { watch } from 'vue'

const props = defineProps(['searchQuery', 'userId'])

// 也正确：通过 props 对象监听（不需要 getter）
watch(() => props.searchQuery, (newValue) => {
  console.log('查询已更改：', newValue)
})

// 监听多个 props
watch(
  () => [props.searchQuery, props.userId],
  ([newQuery, newUserId]) => {
    console.log('Props 已更改：', newQuery, newUserId)
  }
)
</script>
```

## 编写接受 Props 的 Composables

创建应该与解构的 props 一起工作的 composables 时：

```javascript
// composables/useDebounce.js
import { ref, watch, toValue } from 'vue'

export function useDebounce(source, delay = 300) {
  const debounced = ref(toValue(source))  // toValue 处理 getter 和 ref
  let timeout

  watch(
    // toValue 规范化 getter 函数和 refs
    () => toValue(source),
    (newValue) => {
      clearTimeout(timeout)
      timeout = setTimeout(() => {
        debounced.value = newValue
      }, delay)
    }
  )

  return debounced
}
```

```vue
<!-- 用法 -->
<script setup>
const { query } = defineProps(['query'])

// 与 getter 一起工作
const debouncedQuery = useDebounce(() => query, 300)
</script>
```

## Vue 3.5+ 响应式解构

Vue 3.5+ 添加了响应式 props 解构。编译器转换：

```javascript
const { foo } = defineProps(['foo'])
```

为：

```javascript
const __props = defineProps(['foo'])
// foo 在模板中响应式地访问 __props.foo
```

然而，外部函数调用仍然需要 getter，因为 JavaScript 本身传递的是值，而不是引用。

## 参考
- [Vue.js Props - Reactive Props Destructure](https://vuejs.org/guide/components/props.html#reactive-props-destructure)
- [Vue.js Watchers - Watch Source Types](https://vuejs.org/guide/essentials/watchers.html#watch-source-types)
