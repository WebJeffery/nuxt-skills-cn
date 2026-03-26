---
title: Suspense 组件最佳实践
impact: MEDIUM
impactDescription: Suspense 协调异步依赖与回退 UI;配置不当会导致缺少加载状态或令人困惑的 UX
type: best-practice
tags: [vue3, suspense, async-components, async-setup, loading, fallback, router, transition, keepalive]
---

# Suspense 组件最佳实践

**影响: MEDIUM** - `<Suspense>` 协调异步依赖(异步组件或异步设置)并在它们解析时渲染回退。配置不当会导致缺少加载状态、空渲染或微妙的 UX 错误。

## 任务列表

- 将默认和回退插槽内容包装在单个根节点中
- 当您需要回退在恢复时出现时使用 `timeout`
- 当您需要 Suspense 重新触发时使用 `:key` 强制根替换
- 将 `suspensible` 添加到嵌套的 Suspense 边界(Vue 3.3+)
- 使用 `@pending`、`@resolve` 和 `@fallback` 进行程序化加载状态
- 按该顺序嵌套 `RouterView` -> `Transition` -> `KeepAlive` -> `Suspense`
- 在生产中保持 Suspense 使用集中和文档化

## 默认和回退插槽中的单个根

Suspense 在两个插槽中跟踪单个直接子级。将多个元素包装在单个元素或组件中。

**错误:**
```vue
<template>
  <Suspense>
    <AsyncHeader />
    <AsyncList />

    <template #fallback>
      <LoadingSpinner />
      <LoadingHint />
    </template>
  </Suspense>
</template>
```

**正确:**
```vue
<template>
  <Suspense>
    <div>
      <AsyncHeader />
      <AsyncList />
    </div>

    <template #fallback>
      <div>
        <LoadingSpinner />
        <LoadingHint />
      </div>
    </template>
  </Suspense>
</template>
```

## 恢复时的回退时机(`timeout`)

当 Suspense 已经解析并开始新的异步工作时,先前的内容保持可见,直到超时结束。使用 `timeout="0"` 进行立即回退或短延迟以避免闪烁。

**错误:**
```vue
<template>
  <Suspense>
    <component :is="currentView" :key="viewKey" />

    <template #fallback>
      加载中...
    </template>
  </Suspense>
</template>
```

**正确:**
```vue
<template>
  <Suspense :timeout="200">
    <component :is="currentView" :key="viewKey" />

    <template #fallback>
      加载中...
    </template>
  </Suspense>
</template>
```

## 挂起状态仅在根替换时重新触发

一旦解析,Suspense 仅在默认插槽的根节点更改时重新进入挂起。如果异步工作在树的更深处发生,则不会出现回退。

**错误:**
```vue
<template>
  <Suspense>
    <TabContainer>
      <AsyncDashboard v-if="tab === 'dashboard'" />
      <AsyncSettings v-else />
    </TabContainer>

    <template #fallback>
      加载中...
    </template>
  </Suspense>
</template>
```

**正确:**
```vue
<template>
  <Suspense>
    <component :is="tabs[tab]" :key="tab" />

    <template #fallback>
      加载中...
    </template>
  </Suspense>
</template>
```

## 使用 `suspensible` 进行嵌套 Suspense (Vue 3.3+)

嵌套的 Suspense 边界需要 `suspensible` 在内部边界上,以便父级可以协调加载状态。没有它,内部异步内容可能会渲染空节点直到解析。

**错误:**
```vue
<template>
  <Suspense>
    <LayoutShell>
      <Suspense>
        <AsyncWidget />
        <template #fallback>加载小部件...</template>
      </Suspense>
    </LayoutShell>

    <template #fallback>加载布局...</template>
  </Suspense>
</template>
```

**正确:**
```vue
<template>
  <Suspense>
    <LayoutShell>
      <Suspense suspensible>
        <AsyncWidget />
        <template #fallback>加载小部件...</template>
      </Suspense>
    </LayoutShell>

    <template #fallback>加载布局...</template>
  </Suspense>
</template>
```

## 使用 Suspense 事件跟踪加载

使用 `@pending`、`@resolve` 和 `@fallback` 进行分析、全局加载指示器或协调 Suspense 边界之外的 UI。

```vue
<script setup>
import { ref } from 'vue'

const isLoading = ref(false)

const onPending = () => {
  isLoading.value = true
}

const onResolve = () => {
  isLoading.value = false
}
</script>

<template>
  <LoadingBar v-if="isLoading" />

  <Suspense @pending="onPending" @resolve="onResolve">
    <AsyncPage />
    <template #fallback>
      <PageSkeleton />
    </template>
  </Suspense>
</template>
```

## 与 RouterView、Transition、KeepAlive 的推荐嵌套

组合这些组件时,嵌套顺序应该是 `RouterView` -> `Transition` -> `KeepAlive` -> `Suspense`,以便每个包装器正确工作。

**错误:**
```vue
<template>
  <RouterView v-slot="{ Component }">
    <Suspense>
      <KeepAlive>
        <Transition mode="out-in">
          <component :is="Component" />
        </Transition>
      </KeepAlive>
    </Suspense>
  </RouterView>
</template>
```

**正确:**
```vue
<template>
  <RouterView v-slot="{ Component }">
    <Transition mode="out-in">
      <KeepAlive>
        <Suspense>
          <component :is="Component" />
          <template #fallback>加载中...</template>
        </Suspense>
      </KeepAlive>
    </Transition>
  </RouterView>
</template>
```

## 在生产中谨慎对待 Suspense

在生产代码中,保持 Suspense 边界最小,记录它们的使用位置,并拥有回退加载策略,如果您需要替换或重构它们。
