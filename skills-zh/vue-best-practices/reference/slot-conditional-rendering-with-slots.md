---
title: 使用 $slots 条件渲染插槽包装元素
impact: MEDIUM
impactDescription: 当插槽没有内容时，空的包装元素会在 DOM 中渲染，影响布局和样式
type: best-practice
tags: [vue3, slots, conditional-rendering, performance, dom-optimization]
---

# 使用 $slots 条件渲染插槽包装元素

**影响：中等** - 构建带有可选插槽的可重用组件时，即使未提供插槽内容，包装元素（div、section 等）也会渲染。这可能导致布局问题、不必要的 DOM 节点和样式问题。使用 `$slots` 属性仅在插槽有内容时条件渲染包装器。

## 任务清单

- [ ] 在为可选插槽渲染包装元素之前检查 `$slots`
- [ ] 使用 `v-if="$slots.slotName"` 条件渲染包装器
- [ ] 记住 `$slots.default` 检查默认插槽
- [ ] 通过避免空的包装元素保持 DOM 干净

**错误：**
```vue
<!-- Card.vue -->
<template>
  <div class="card">
    <!-- 错误：即使没有内容，页眉包装器也会渲染 -->
    <div class="card-header">
      <slot name="header" />
    </div>

    <!-- 错误：带有内边距/外边距的空 div 影响布局 -->
    <div class="card-body">
      <slot />
    </div>

    <!-- 错误：页脚渲染为空元素 -->
    <div class="card-footer">
      <slot name="footer" />
    </div>
  </div>
</template>

<style scoped>
.card-header { padding: 16px; border-bottom: 1px solid #eee; }
.card-footer { padding: 16px; border-top: 1px solid #eee; }
</style>
```

当未使用所有插槽时：
```vue
<!-- 结果为带有边框的空 .card-header 和 .card-footer div -->
<Card>
  <p>只有主体内容</p>
</Card>
```

**正确：**
```vue
<!-- Card.vue -->
<template>
  <div class="card">
    <!-- 正确：仅当插槽有内容时才渲染页眉包装器 -->
    <div v-if="$slots.header" class="card-header">
      <slot name="header" />
    </div>

    <!-- 正确：检查默认插槽 -->
    <div v-if="$slots.default" class="card-body">
      <slot />
    </div>

    <!-- 正确：仅当提供时才渲染页脚 -->
    <div v-if="$slots.footer" class="card-footer">
      <slot name="footer" />
    </div>
  </div>
</template>

<style scoped>
.card-header { padding: 16px; border-bottom: 1px solid #eee; }
.card-footer { padding: 16px; border-top: 1px solid #eee; }
</style>
```

现在 DOM 干净：
```vue
<!-- 仅渲染 .card-body，没有空的页眉/页脚 -->
<Card>
  <p>只有主体内容</p>
</Card>
```

## 高级：在 Script Setup 中使用 $slots

```vue
<script setup>
import { useSlots, computed } from 'vue'

const slots = useSlots()

const hasHeader = computed(() => !!slots.header)
const hasFooter = computed(() => !!slots.footer)

// 也可以在逻辑中条件检查插槽内容
function getLayoutClass() {
  if (slots.header && slots.footer) return 'full-layout'
  if (slots.header) return 'header-only'
  if (slots.footer) return 'footer-only'
  return 'minimal'
}
</script>

<template>
  <div :class="['card', getLayoutClass()]">
    <header v-if="hasHeader">
      <slot name="header" />
    </header>
    <main>
      <slot />
    </main>
    <footer v-if="hasFooter">
      <slot name="footer" />
    </footer>
  </div>
</template>
```

## 何时使用此模式

| 场景 | 使用 $slots 检查？ |
|----------|-------------------|
| 包装器有样式（内边距、边框、背景） | 是 |
| 包装器影响布局（flex、grid） | 是 |
| 插槽总是预期有内容 | 否 |
| 插槽周围没有包装元素 | 否 |
| 包装器纯粹是语义性的（无视觉影响） | 可选 |

## 参考
- [Vue.js 插槽 - 条件插槽](https://vuejs.org/guide/components/slots.html#conditional-slots)
