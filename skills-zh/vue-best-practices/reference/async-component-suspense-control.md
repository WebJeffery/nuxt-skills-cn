# 异步组件默认可挂起

## 规则

使用 `defineAsyncComponent` 创建的异步组件会自动被视为任何父级 `<Suspense>` 组件的异步依赖。当被 `<Suspense>` 包裹时，异步组件自己的 `loadingComponent`、`errorComponent`、`delay` 和 `timeout` 选项会被忽略。

## 为什么这很重要

这种行为会导致混淆，因为开发者在异步组件上配置加载和错误状态，但这些状态从未出现，因为父级 `<Suspense>` 接管了控制。组件的选项被静默忽略，导致意外行为。

## 坏代码

```vue
<script setup>
import { defineAsyncComponent } from 'vue'

// 如果存在父级 Suspense，这些选项将被忽略
const AsyncDashboard = defineAsyncComponent({
  loader: () => import('./Dashboard.vue'),
  loadingComponent: LoadingSpinner,  // 不会显示！
  errorComponent: ErrorDisplay,       // 不会显示！
  timeout: 3000                        // 被忽略！
})
</script>

<template>
  <!-- 如果这在树中某处的 Suspense 内部 -->
  <AsyncDashboard />
</template>
```

## 好代码

```vue
<script setup>
import { defineAsyncComponent } from 'vue'

// 使用 suspensible: false 保持对加载/错误状态的控制
const AsyncDashboard = defineAsyncComponent({
  loader: () => import('./Dashboard.vue'),
  loadingComponent: LoadingSpinner,
  errorComponent: ErrorDisplay,
  timeout: 3000,
  suspensible: false  // 组件控制自己的加载状态
})
</script>

<template>
  <AsyncDashboard />
</template>
```

## 何时使用每种方法

**保持 suspensible（默认）** 当：
- 您希望在布局级别集中处理加载/错误
- 父级 `<Suspense>` 提供适当的反馈
- 多个异步组件应显示统一的加载状态

**使用 `suspensible: false`** 当：
- 您需要特定于组件的加载指示器
- 组件应该处理自己的错误状态
- 您想要对 UX 进行细粒度控制

## 关键点

1. 在依赖异步组件选项之前，检查组件树是否有 `<Suspense>` 祖先
2. 当您需要组件管理自己的状态时，显式使用 `suspensible: false`
3. `<Suspense>` 组件的 `#fallback` 插槽和 `onErrorCaptured` 优先于异步组件选项

## 参考

- [Vue.js 异步组件文档](https://vuejs.org/guide/components/async)
- [Vue.js Suspense 文档](https://vuejs.org/guide/built-ins/suspense)
