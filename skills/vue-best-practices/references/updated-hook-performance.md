---
title: updated 钩子性能
impact: HIGH
impactDescription: updated 钩子会在每次渲染后触发,误用会导致性能问题和无限循环
type: best-practice
tags: [vue3, lifecycle, updated, performance, watch]
---

# updated 钩子性能

**影响: HIGH** - `updated` 钩子会在每次组件更新后触发。误用会导致性能问题和无限循环。优先使用 `watch` 或 `watchEffect` 进行响应式副作用。

## 任务列表

- 避免在 `updated` 钩子中进行 DOM 操作
- 使用 `watch` 或 `watchEffect` 进行响应式副作用
- 在 `updated` 钩子中避免修改响应式状态
- 使用 `nextTick` 等待 DOM 更新完成
- 考虑使用 `watchPostEffect` 在 DOM 更新后执行

## 错误示例

**错误:**
```vue
<script setup>
import { ref, onUpdated } from 'vue'

const count = ref(0)

onUpdated(() => {
  // 这会导致无限循环
  count.value++
})
</script>
```

**错误:**
```vue
<script setup>
import { onUpdated } from 'vue'

onUpdated(() => {
  // 在 updated 中进行 DOM 操作是不必要的
  document.querySelector('.title').textContent = '新标题'
})
</script>
```

## 正确示例

**使用 watch:**
```vue
<script setup>
import { ref, watch } from 'vue'

const count = ref(0)

watch(count, (newValue) => {
  console.log(`Count changed to ${newValue}`)
})
</script>
```

**使用 watchEffect:**
```vue
<script setup>
import { ref, watchEffect } from 'vue'

const count = ref(0)

watchEffect(() => {
  console.log(`Count is ${count.value}`)
})
</script>
```

## 使用 nextTick

**正确:**
```vue
<script setup>
import { ref, nextTick } from 'vue'

const count = ref(0)

async function increment() {
  count.value++
  await nextTick()
  // DOM 已更新
  console.log('DOM updated')
}
</script>
```

## 使用 watchPostEffect

**正确:**
```vue
<script setup>
import { ref, watchPostEffect } from 'vue'

const count = ref(0)

watchPostEffect(() => {
  // 在 DOM 更新后执行
  console.log('DOM updated, count is', count.value)
})
</script>
```

## 最佳实践

1. **避免使用 updated:**
   - `updated` 钩子会在每次更新后触发
   - 很难控制触发时机
   - 容易导致性能问题

2. **使用 watch 进行响应式副作用:**
   - `watch` 只在依赖项变化时触发
   - 更精确的控制
   - 更好的性能

3. **使用 watchEffect 进行即时响应:**
   - `watchEffect` 立即执行并追踪依赖
   - 自动收集依赖
   - 适合需要立即执行的场景

4. **使用 nextTick 等待 DOM 更新:**
   - `nextTick` 确保在下一次 DOM 更新后执行
   - 更可靠的 DOM 操作时机
   - 避免在渲染过程中修改 DOM

5. **使用 watchPostEffect 进行 DOM 后操作:**
   - `watchPostEffect` 在 DOM 更新后执行
   - 适合需要访问更新后 DOM 的场景
   - 比 `updated` 更可控

## 性能影响

- `updated` 钩子会在每次渲染后触发
- 在 `updated` 中进行复杂操作会严重影响性能
- 在 `updated` 中修改状态会导致无限循环
- 使用 `watch` 或 `watchEffect` 可以避免不必要的执行

## 替代方案

| 需求 | 推荐方案 |
|------|---------|
| 响应式副作用 | `watch` 或 `watchEffect` |
| DOM 更新后操作 | `nextTick` 或 `watchPostEffect` |
| 组件挂载后操作 | `onMounted` |
| 组件卸载前操作 | `onBeforeUnmount` |
