---
title: Teleport 组件最佳实践
impact: MEDIUM
impactDescription: Teleport 在组件的 DOM 位置之外渲染内容,这对于覆盖层是必需的,但会影响样式和布局
type: best-practice
tags: [vue3, teleport, modal, overlay, positioning, responsive]
---

# Teleport 组件最佳实践

**影响: MEDIUM** - `<Teleport>` 在 DOM 中的不同位置渲染组件模板的一部分,同时保留 Vue 组件层次结构。将其用于覆盖层(模态框、通知、工具提示)或必须逃脱堆叠上下文、溢出或固定定位约束的任何 UI。

## 任务列表

- 将覆盖层 Teleport 到 `body` 或应用根之外的专用容器
- 为相似的 UI 保持共享目标(`#modals`、`#notifications`)并通过顺序或 z-index 控制分层
- 使用 `:disabled` 进行应该在小屏幕上内联渲染的响应式布局
- 记住 props、emits 和 provide/inject 仍然通过 teleport 工作
- 避免依赖父堆叠上下文或变换进行 teleported UI

## 将覆盖层 Teleport 出变换的容器

当祖先具有 `transform`、`filter` 或 `perspective` 时,固定定位的覆盖层可能表现得像它们是局部定位的。Teleport 逃脱该上下文。

**错误:**
```vue
<template>
  <div class="animated-container">
    <button @click="open = true">打开</button>

    <!-- 错误:固定定位限定于变换的父级 -->
    <div v-if="open" class="modal">模态框</div>
  </div>
</template>

<style>
.animated-container {
  transform: translateZ(0);
}

.modal {
  position: fixed;
  inset: 0;
  z-index: 9999;
}
</style>
```

**正确:**
```vue
<template>
  <div class="animated-container">
    <button @click="open = true">打开</button>

    <Teleport to="body">
      <div v-if="open" class="modal">模态框</div>
    </Teleport>
  </div>
</template>
```

## 使用 `disabled` 进行响应式布局

使用 `:disabled` 在移动设备上内联渲染,在较大屏幕上 teleport:

```vue
<script setup>
import { useMediaQuery } from '@vueuse/core'

const isMobile = useMediaQuery('(max-width: 768px)')
</script>

<template>
  <Teleport to="body" :disabled="isMobile">
    <nav class="sidebar">导航</nav>
  </Teleport>
</template>
```

## 逻辑层次结构保留

Teleport 更改 DOM 位置,而不是 Vue 组件树。Props、emits、slots 和 provide/inject 仍然工作:

```vue
<template>
  <Teleport to="body">
    <ChildPanel :message="message" @close="open = false" />
  </Teleport>
</template>
```

## 多个 Teleport 到同一目标

Teleport 到同一目标按声明顺序追加:

```vue
<template>
  <Teleport to="#notifications">
    <div>第一个</div>
  </Teleport>

  <Teleport to="#notifications">
    <div>第二个</div>
  </Teleport>
</template>
```

使用共享容器保持堆叠可预测,并仅在需要显式分层时应用 z-index。
