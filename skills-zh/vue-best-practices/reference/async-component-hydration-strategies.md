# 异步组件延迟水合策略（Vue 3.5+）

## 规则

在 Vue 3.5+ 中，使用异步组件的水合策略来控制 SSR 渲染的组件何时变为交互式。单独导入水合策略以进行 tree-shaking。

## 为什么这很重要

在 SSR 应用程序中，立即水合所有组件可能会阻塞主线程并延迟交互性。延迟水合允许非关键组件仅在需要时变为交互式，从而改善 Time to Interactive (TTI) 指标。

## 可用的水合策略

### hydrateOnIdle

使用 `requestIdleCallback` 在浏览器空闲时水合：

```vue
<script setup>
import { defineAsyncComponent, hydrateOnIdle } from 'vue'

// 适用于非关键、折叠下方的内容
const AsyncFooter = defineAsyncComponent({
  loader: () => import('./Footer.vue'),
  hydrate: hydrateOnIdle()
})

// 带有最大超时（以防空闲永远不会发生）
const AsyncSidebar = defineAsyncComponent({
  loader: () => import('./Sidebar.vue'),
  hydrate: hydrateOnIdle(5000) // 最多 5 秒
})
</script>
```

### hydrateOnVisible

当元素通过 `IntersectionObserver` 进入视口时水合：

```vue
<script setup>
import { defineAsyncComponent, hydrateOnVisible } from 'vue'

// 适用于折叠下方的内容
const AsyncComments = defineAsyncComponent({
  loader: () => import('./Comments.vue'),
  hydrate: hydrateOnVisible()
})

// 带有根边距以更早水合
const AsyncRelatedPosts = defineAsyncComponent({
  loader: () => import('./RelatedPosts.vue'),
  hydrate: hydrateOnVisible({ rootMargin: '100px' })
})
</script>
```

### hydrateOnMediaQuery

当媒体查询匹配时水合：

```vue
<script setup>
import { defineAsyncComponent, hydrateOnMediaQuery } from 'vue'

// 仅在移动设备上水合
const AsyncMobileMenu = defineAsyncComponent({
  loader: () => import('./MobileMenu.vue'),
  hydrate: hydrateOnMediaQuery('(max-width: 768px)')
})

// 仅在桌面上水合
const AsyncDesktopSidebar = defineAsyncComponent({
  loader: () => import('./DesktopSidebar.vue'),
  hydrate: hydrateOnMediaQuery('(min-width: 1024px)')
})
</script>
```

### hydrateOnInteraction

当用户与组件交互时水合：

```vue
<script setup>
import { defineAsyncComponent, hydrateOnInteraction } from 'vue'

// 在点击时水合
const AsyncDropdown = defineAsyncComponent({
  loader: () => import('./Dropdown.vue'),
  hydrate: hydrateOnInteraction('click')
})

// 在多个事件上水合
const AsyncTooltip = defineAsyncComponent({
  loader: () => import('./Tooltip.vue'),
  hydrate: hydrateOnInteraction(['mouseover', 'focus'])
})
</script>
```

**注意**: 触发事件在水合完成后重放，因此用户交互不会丢失。

## 自定义水合策略

```typescript
import { defineAsyncComponent, type HydrationStrategy } from 'vue'

const hydrateAfterAnimation: HydrationStrategy = (hydrate, forEachElement) => {
  // 等待页面加载动画完成
  const timeout = setTimeout(hydrate, 1000)

  return () => clearTimeout(timeout) // 清理
}

const AsyncWidget = defineAsyncComponent({
  loader: () => import('./Widget.vue'),
  hydrate: hydrateAfterAnimation
})
```

## 关键点

1. 水合策略仅适用于 SSR - 在仅客户端应用中没有效果
2. 单独导入策略: `import { hydrateOnIdle } from 'vue'`
3. `hydrateOnInteraction` 在水合后重放触发事件
4. 对折叠下方的内容使用 `hydrateOnVisible`
5. 对非关键组件使用 `hydrateOnIdle`
6. 对设备特定组件使用 `hydrateOnMediaQuery`

## 策略选择指南

| 组件类型 | 推荐策略 |
|----------------|---------------------|
| 页脚、相关内容 | `hydrateOnIdle` |
| 折叠下方部分 | `hydrateOnVisible` |
| 交互式小部件 | `hydrateOnInteraction` |
| 仅移动端组件 | `hydrateOnMediaQuery` |
| 关键的折叠上方内容 | 无策略（立即） |

## 参考

- [Vue.js 异步组件文档](https://vuejs.org/guide/components/async)
