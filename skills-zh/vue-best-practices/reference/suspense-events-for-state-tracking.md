# 使用 Suspense 事件进行加载状态跟踪

## 规则

`<Suspense>` 发出三个事件: `pending`、`resolve` 和 `fallback`。使用这些事件来跟踪加载状态、实现分析、协调 UI 更新或在适当时间触发副作用。

## 为什么这很重要

仅依赖回退插槽进行加载指示会限制你的选项。这些事件提供对 Suspense 状态更改的编程访问，使你能够:
- 加载进度跟踪
- 感知性能的分析
- 协调的动画
- Suspense 边界之外的自定义加载指示器
- 调试异步组件行为

## 基本用法

```vue
<script setup>
import { ref } from 'vue'

const loadingState = ref('idle')
const loadStartTime = ref(null)

const onPending = () => {
  loadingState.value = 'pending'
  loadStartTime.value = Date.now()
  console.log('Suspense 进入待定状态')
}

const onResolve = () => {
  loadingState.value = 'resolved'
  const loadTime = Date.now() - loadStartTime.value
  console.log(`内容在 ${loadTime}ms 内解决`)

  // 跟踪性能
  analytics.track('page_load_time', { duration: loadTime })
}

const onFallback = () => {
  loadingState.value = 'fallback'
  console.log('回退内容现在可见')
}
</script>

<template>
  <Suspense
    @pending="onPending"
    @resolve="onResolve"
    @fallback="onFallback"
  >
    <AsyncPage />
    <template #fallback>
      <LoadingSkeleton />
    </template>
  </Suspense>
</template>
```

## 高级: 全局加载指示器

```vue
<!-- App.vue -->
<script setup>
import { ref, provide } from 'vue'

const globalLoading = ref(false)
const pendingCount = ref(0)

provide('globalLoading', globalLoading)

const onSuspensePending = () => {
  pendingCount.value++
  globalLoading.value = true
}

const onSuspenseResolve = () => {
  pendingCount.value--
  if (pendingCount.value === 0) {
    globalLoading.value = false
  }
}
</script>

<template>
  <!-- 全局加载栏 -->
  <Transition name="fade">
    <LoadingBar v-if="globalLoading" />
  </Transition>

  <RouterView v-slot="{ Component }">
    <Suspense
      @pending="onSuspensePending"
      @resolve="onSuspenseResolve"
    >
      <component :is="Component" />
      <template #fallback>
        <PageSkeleton />
      </template>
    </Suspense>
  </RouterView>
</template>
```

## 事件序列

```
初始加载:
1. @pending - Suspense 进入待定状态
2. @fallback - 回退内容显示（如果快速解决可能不会触发）
3. @resolve - 异步内容准备就绪

内容更改（带超时）:
1. @pending - 检测到新的异步依赖
2. （之前的内容在超时期间仍然可见）
3. @fallback - 超时超过，显示回退
4. @resolve - 新内容准备就绪

内容更改（快速）:
1. @pending - 检测到新的异步依赖
2. @resolve - 在超时之前解决，回退从未显示
```

## 与过渡协调

```vue
<script setup>
import { ref } from 'vue'

const isTransitioning = ref(false)

const onPending = () => {
  isTransitioning.value = true
}

const onResolve = () => {
  // 延迟以允许进入过渡完成
  setTimeout(() => {
    isTransitioning.value = false
  }, 300)
}
</script>

<template>
  <div :class="{ 'is-loading': isTransitioning }">
    <Transition mode="out-in" @after-leave="onAfterLeave">
      <Suspense @pending="onPending" @resolve="onResolve">
        <component :is="currentView" :key="viewKey" />
        <template #fallback>
          <LoadingView />
        </template>
      </Suspense>
    </Transition>
  </div>
</template>
```

## 关键点

1. `@pending` 在 Suspense 开始等待异步依赖时触发
2. `@fallback` 在回退内容变为可见时触发（尊重超时）
3. `@resolve` 在默认插槽内容准备显示时触发
4. 使用事件进行分析、调试和协调 Suspense 之外的 UI
5. 事件在多个 Suspense 边界之间启用全局加载指示器
6. 如果内容在超时之前解决，`@fallback` 可能不会触发

## 参考

- [Vue.js Suspense 事件](https://vuejs.org/guide/built-ins/suspense#events)
