# Suspense 插槽只允许一个直接子元素

## 规则

`<Suspense>` 的 `#default` 和 `#fallback` 插槽都只允许一个直接子节点。多个根元素将导致意外行为。

## 为什么这很重要

Vue 的 Suspense 需要跟踪单个根节点来管理待定/已解决状态转换。默认插槽中的多个子元素可能导致渲染问题或 Suspense 无法正常工作。

## 不好的代码

```vue
<template>
  <!-- 默认插槽中有多个子元素 - 有问题 -->
  <Suspense>
    <AsyncHeader />
    <AsyncContent />
    <AsyncFooter />

    <template #fallback>
      <LoadingHeader />
      <LoadingContent />
    </template>
  </Suspense>
</template>
```

## 好的代码

```vue
<template>
  <!-- 每个插槽中的单个根包装器 -->
  <Suspense>
    <div class="page-container">
      <AsyncHeader />
      <AsyncContent />
      <AsyncFooter />
    </div>

    <template #fallback>
      <div class="loading-container">
        <LoadingHeader />
        <LoadingContent />
      </div>
    </template>
  </Suspense>
</template>
```

## 替代方案: 专用布局组件

```vue
<!-- PageLayout.vue - 包含所有异步组件 -->
<script setup>
import AsyncHeader from './AsyncHeader.vue'
import AsyncContent from './AsyncContent.vue'
import AsyncFooter from './AsyncFooter.vue'
</script>

<template>
  <div class="page-layout">
    <AsyncHeader />
    <AsyncContent />
    <AsyncFooter />
  </div>
</template>
```

```vue
<!-- 使用 -->
<template>
  <Suspense>
    <PageLayout />
    <template #fallback>
      <SkeletonLayout />
    </template>
  </Suspense>
</template>
```

## 关键点

1. 始终将多个元素包装在单个容器元素中
2. 这适用于 `#default` 和 `#fallback` 插槽
3. 考虑将复杂布局提取到专用组件中
4. 单个子元素要求帮助 Vue 正确跟踪组件树

## 参考

- [Vue.js Suspense 文档](https://vuejs.org/guide/built-ins/suspense)
