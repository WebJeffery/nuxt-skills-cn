---
title: 异步组件最佳实践
impact: MEDIUM
impactDescription: 不当的异步组件策略会延迟 SSR 应用的交互性并创建加载 UI 闪烁
type: best-practice
tags: [vue3, async-components, ssr, hydration, performance, ux]
---

# 异步组件最佳实践

**影响: MEDIUM** - 异步组件应减少 JavaScript 成本而不降低感知性能。专注于 SSR 中的水合时机和稳定的加载 UX。

## 任务列表

- 对非关键 SSR 组件树使用延迟水合策略
- 仅导入实际使用的水合辅助函数
- 保持 `loadingComponent` 延迟接近默认的 `200ms`,除非真实的 UX 数据建议其他值
- 配置 `delay` 和 `timeout` 以获得可预测的加载行为

## 在 SSR 中使用延迟水合策略

在 Vue 3.5+ 中,异步组件可以延迟水合直到空闲时间、可见性、媒体查询匹配或用户交互。

**错误:**
```vue
<script setup lang="ts">
import { defineAsyncComponent } from 'vue'

const AsyncComments = defineAsyncComponent({
  loader: () => import('./Comments.vue')
})
</script>
```

**正确:**
```vue
<script setup lang="ts">
import {
  defineAsyncComponent,
  hydrateOnVisible,
  hydrateOnIdle
} from 'vue'

const AsyncComments = defineAsyncComponent({
  loader: () => import('./Comments.vue'),
  hydrate: hydrateOnVisible({ rootMargin: '100px' })
})

const AsyncFooter = defineAsyncComponent({
  loader: () => import('./Footer.vue'),
  hydrate: hydrateOnIdle(5000)
})
</script>
```

## 防止加载微调器闪烁

避免为通常快速解析的组件立即显示加载 UI。

**错误:**
```vue
<script setup lang="ts">
import { defineAsyncComponent } from 'vue'
import LoadingSpinner from './LoadingSpinner.vue'

const AsyncDashboard = defineAsyncComponent({
  loader: () => import('./Dashboard.vue'),
  loadingComponent: LoadingSpinner,
  delay: 0
})
</script>
```

**正确:**
```vue
<script setup lang="ts">
import { defineAsyncComponent } from 'vue'
import LoadingSpinner from './LoadingSpinner.vue'
import ErrorDisplay from './ErrorDisplay.vue'

const AsyncDashboard = defineAsyncComponent({
  loader: () => import('./Dashboard.vue'),
  loadingComponent: LoadingSpinner,
  errorComponent: ErrorDisplay,
  delay: 200,
  timeout: 30000
})
</script>
```

## 延迟指南

| 场景 | 推荐延迟 |
|----------|-------------------|
| 小型组件,快速网络 | `200ms` |
| 已知的繁重组件 | `100ms` |
| 后台或非关键 UI | `300-500ms` |
