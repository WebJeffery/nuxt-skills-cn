---
title: Suspense 是实验性的，API 可能会更改
impact: MEDIUM
impactDescription: 实验性 API 可能会在没有警告的情况下更改，导致生产应用程序中的破坏性更改
type: warning
tags: [vue3, suspense, experimental, api-stability, breaking-changes]
---

# Suspense 是实验性的，API 可能会更改

**影响：中等** - `<Suspense>` 是 Vue 3 中的实验性功能。API 不保证达到稳定状态，在此之前可能会更改。在生产应用程序中使用 Suspense 时，请计划潜在的破坏性更改。

## 规则

在生产代码中严重依赖实验性功能会产生维护风险。如果 API 更改，重构可能是巨大的。自 Vue 3 的初始发布（超过 4 年）以来，Suspense API 一直是实验性的，开发人员报告了对错误和限制的挫败感。

## 不好的代码

```vue
<script setup>
// 在实验性 API 上构建关键的应用程序基础设施
// 没有回退策略
</script>

<template>
  <!-- 核心导航完全依赖于 Suspense -->
  <Suspense>
    <RouterView />
    <template #fallback>
      <GlobalLoadingScreen />
    </template>
  </Suspense>
</template>
```

## 好的代码

```vue
<script setup>
import { ref } from 'vue'

// 准备回退加载策略
const isLoading = ref(false)

// 如果需要，可以在 Suspense 和手动加载之间切换
const useSuspense = true
</script>

<template>
  <!-- 记录这使用了实验性 API -->
  <!-- 考虑: Suspense 是实验性的 - 监控 Vue 发布 -->
  <Suspense v-if="useSuspense">
    <RouterView />
    <template #fallback>
      <GlobalLoadingScreen />
    </template>
  </Suspense>

  <!-- 替代加载策略可用 -->
  <template v-else>
    <GlobalLoadingScreen v-if="isLoading" />
    <RouterView v-else />
  </template>
</template>
```

## 缓解策略

1. **隔离 Suspense 使用** - 不要在整个代码库中传播 Suspense；将其集中化
2. **记录使用** - 在使用 Suspense 的地方注释以便于重构
3. **彻底测试** - Suspense 边缘情况在不同 Vue 版本中的行为可能不同
4. **关注 Vue 发布** - 监控更改日志以了解 Suspense 相关的更改
5. **有回退计划** - 准备在需要时实现手动加载状态

## 关键点

1. Suspense 已经是实验性的 4+ 年，没有宣布稳定的发布日期
2. 一些开发人员发现它对于某些用例"太 buggy 或太受限"
3. 考虑 Vue 2 风格的加载模式作为关键路径的可靠替代方案
4. 保持 Suspense 边界最小化和集中化以便于迁移

## 参考

- [Vue.js Suspense 文档](https://vuejs.org/guide/built-ins/suspense)
- [Vue RFC 讨论 #746](https://github.com/vuejs/rfcs/discussions/746)
