---
name: vue
description: Vue 3 Composition API、script setup 宏、响应式系统和内置组件。在编写 Vue SFC、defineProps/defineEmits/defineModel、侦听器或使用 Transition/Teleport/Suspense/KeepAlive 时使用。
metadata:
  author: Anthony Fu
  version: "2026.1.31"
  source: Generated from https://github.com/vuejs/docs, scripts at https://github.com/antfu/skills
---

# Vue

> 基于 Vue 3.5。始终使用 Composition API 和 `<script setup lang="ts">`。

## 偏好

- 优先使用 TypeScript 而非 JavaScript
- 优先使用 `<script setup lang="ts">` 而非 `<script>`
- 为了性能，如果不需要深度响应式，优先使用 `shallowRef` 而非 `ref`
- 始终使用 Composition API 而非 Options API
- 不鼓励使用响应式 Props 解构

## 核心

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| Script Setup & 宏 | `<script setup>`、defineProps、defineEmits、defineModel、defineExpose、defineOptions、defineSlots、泛型 | [script-setup-macros](references/script-setup-macros.md) |
| 响应式 & 生命周期 | ref、shallowRef、computed、watch、watchEffect、effectScope、生命周期钩子、组合式函数 | [core-new-apis](references/core-new-apis.md) |

## 功能

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 内置组件 & 指令 | Transition、Teleport、Suspense、KeepAlive、v-memo、自定义指令 | [advanced-patterns](references/advanced-patterns.md) |

## 最佳实践

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 性能优化 | 响应式优化、渲染优化、组件优化、事件优化、内存优化 | [best-practices-performance](references/best-practices-performance.md) |
| 开发体验 | TypeScript 集成、代码组织、开发工具、HMR、代码片段、测试、文档 | [best-practices-dev-experience](references/best-practices-dev-experience.md) |
| 调试技巧 | Vue DevTools、控制台调试、响应式调试、组件调试、性能调试、错误处理、网络调试 | [best-practices-debugging](references/best-practices-debugging.md) |
| 常见问题 | 响应式问题、组件问题、生命周期问题、性能问题、类型问题、构建问题 | [best-practices-troubleshooting](references/best-practices-troubleshooting.md) |

## 快速参考

### 组件模板

```vue
<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'

const props = defineProps<{
  title: string
  count?: number
}>()

const emit = defineEmits<{
  update: [value: string]
}>()

const model = defineModel<string>()

const doubled = computed(() => (props.count ?? 0) * 2)

watch(() => props.title, (newVal) => {
  console.log('Title changed:', newVal)
})

onMounted(() => {
  console.log('Component mounted')
})
</script>

<template>
  <div>{{ title }} - {{ doubled }}</div>
</template>
```

### 关键导入

```ts
// 响应式
import { computed, reactive, readonly, ref, shallowRef, toRef, toRefs, toValue } from 'vue'

// 侦听器
import { onWatcherCleanup, watch, watchEffect, watchPostEffect } from 'vue'

// 生命周期
import { onBeforeMount, onBeforeUnmount, onBeforeUpdate, onMounted, onUnmounted, onUpdated } from 'vue'

// 工具函数
import { defineAsyncComponent, defineComponent, nextTick } from 'vue'
```
