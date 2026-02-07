---
title: 考虑使用组合式函数替代无渲染组件
impact: MEDIUM
impactDescription: 与组合式函数相比，无渲染插槽组件在纯逻辑复用上会增加开销
type: best-practice
tags: [vue3, slots, renderless-components, composables, performance, composition-api]
---

# 考虑使用组合式函数替代无渲染组件

**影响：中等** - 无渲染组件（仅通过作用域插槽暴露逻辑而不渲染自己 UI 的组件）是 Vue 2 中流行的逻辑复用模式。在 Vue 3 中，对于相同的用例，组合式 API 的组合式函数通常更高效、更简单。

## 任务清单

- [ ] 评估组合式函数是否可以替代无渲染组件
- [ ] 当真正需要基于插槽的组合时使用无渲染组件
- [ ] 对于纯逻辑/状态共享优先使用组合式函数
- [ ] 考虑组件开销与函数开销

**无渲染组件模式：**
```vue
<!-- MouseTracker.vue - 无渲染组件 -->
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const x = ref(0)
const y = ref(0)

function update(event) {
  x.value = event.pageX
  y.value = event.pageY
}

onMounted(() => window.addEventListener('mousemove', update))
onUnmounted(() => window.removeEventListener('mousemove', update))
</script>

<template>
  <!-- 仅渲染插槽，没有自己的 DOM -->
  <slot :x="x" :y="y" />
</template>
```

```vue
<!-- 使用 -->
<MouseTracker v-slot="{ x, y }">
  <div>鼠标: {{ x }}, {{ y }}</div>
</MouseTracker>
```

**组合式函数替代方案（推荐）：**
```typescript
// composables/useMouse.ts
import { ref, onMounted, onUnmounted } from 'vue'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  function update(event: MouseEvent) {
    x.value = event.pageX
    y.value = event.pageY
  }

  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  return { x, y }
}
```

```vue
<!-- 使用 - 更简单，无需额外组件 -->
<script setup>
import { useMouse } from '@/composables/useMouse'

const { x, y } = useMouse()
</script>

<template>
  <div>鼠标: {{ x }}, {{ y }}</div>
</template>
```

## 何时使用每种模式

| 模式 | 最适合 |
|---------|----------|
| **组合式函数** | 纯逻辑复用、状态管理、副作用 |
| **无渲染组件** | 当需要插槽组合、具有灵活子元素的包装逻辑时 |

### 使用组合式函数时：
- 共享响应式状态和方法
- 封装副作用（事件监听器、定时器）
- 不需要模板插槽组合
- 需要最大性能时

### 使用无渲染组件时：
- 需要为任意插槽内容提供上下文/数据
- 构建提供者组件（如上下文提供者）
- 插槽组合模式确实有用时
- 构建具有最大灵活性的组件库

## 真实示例

**组合式函数更好：**
```typescript
// 数据获取
export function useFetch<T>(url: string) {
  const data = ref<T | null>(null)
  const error = ref<Error | null>(null)
  const loading = ref(true)
  // ... 获取逻辑
  return { data, error, loading }
}

// 表单验证
export function useForm(initialValues) {
  const values = ref(initialValues)
  const errors = ref({})
  const validate = () => { /* ... */ }
  return { values, errors, validate }
}
```

**无渲染组件更好：**
```vue
<!-- 需要包装任意项渲染的虚拟滚动列表 -->
<VirtualScroller :items="items" :item-height="50">
  <template #default="{ item, style }">
    <!-- 消费者控制每个项的渲染方式 -->
    <div :style="style" class="custom-item">
      {{ item.name }}
    </div>
  </template>
</VirtualScroller>
```

## 性能考虑

无渲染组件仍然：
- 创建组件实例
- 经过 Vue 组件生命周期
- 具有响应式开销

组合式函数只是函数：
- 没有组件实例开销
- 直接的响应式原语
- 更小的包大小

## 参考
- [Vue.js 插槽 - 无渲染组件](https://vuejs.org/guide/components/slots.html#renderless-components)
- [Vue.js 组合式函数](https://vuejs.org/guide/reusability/composables.html)
