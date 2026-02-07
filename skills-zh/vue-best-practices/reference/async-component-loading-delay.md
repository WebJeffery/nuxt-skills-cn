# 异步组件加载延迟以防止闪烁

## 规则

在使用 `loadingComponent` 配置异步组件时，使用 `delay` 选项（默认 200ms）。这可以防止在快速网络上组件快速加载时出现 UI 闪烁。

## 为什么这很重要

如果没有延迟，当异步组件快速加载时，加载组件会短暂出现并立即消失。这会产生刺眼的"闪烁"效果，降低用户体验。选择 200ms 默认值是因为比这更快的加载被感知为即时。

## 错误代码

```vue
<script setup>
import { defineAsyncComponent } from 'vue'
import LoadingSpinner from './LoadingSpinner.vue'

const AsyncDashboard = defineAsyncComponent({
  loader: () => import('./Dashboard.vue'),
  loadingComponent: LoadingSpinner,
  delay: 0  // 加载旋转器立即闪烁，导致闪烁
})
</script>
```

## 正确代码

```vue
<script setup>
import { defineAsyncComponent } from 'vue'
import LoadingSpinner from './LoadingSpinner.vue'

const AsyncDashboard = defineAsyncComponent({
  loader: () => import('./Dashboard.vue'),
  loadingComponent: LoadingSpinner,
  // delay: 200 是默认值，但你可以根据 UX 需求进行调整
})
</script>
```

```vue
<script setup>
import { defineAsyncComponent } from 'vue'
import LoadingSpinner from './LoadingSpinner.vue'
import ErrorDisplay from './ErrorDisplay.vue'

// 对于较慢的预期加载，考虑更短的延迟
const AsyncHeavyChart = defineAsyncComponent({
  loader: () => import('./HeavyChart.vue'),
  loadingComponent: LoadingSpinner,
  errorComponent: ErrorDisplay,
  delay: 100,    // 对于已知繁重的组件更早显示加载
  timeout: 30000 // 复杂组件的超时时间更长
})
</script>
```

## 选择正确的延迟

| 场景 | 推荐延迟 |
|----------|-------------------|
| 快速网络，小组件 | 200ms（默认） |
| 已知繁重组件 | 100ms |
| 用户正在等待的交互元素 | 50-100ms |
| 后台内容加载 | 300-500ms |

## 关键点

1. 默认 200ms 延迟是大多数情况的好选择
2. 永远不要设置 `delay: 0`，除非你明确希望立即显示加载状态
3. 将 `delay` 与 `timeout` 配对以进行完整的加载状态管理
4. 在调整延迟时考虑你的网络条件和组件大小

## 参考

- [Vue.js 异步组件文档](https://vuejs.org/guide/components/async)
