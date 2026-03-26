---
name: built-in-components
description: NuxtLink、NuxtPage、NuxtLayout 和其他内置 Nuxt 组件
---

# 内置组件

Nuxt 提供几个内置组件用于常见功能。

## NuxtLink

具有预取功能的优化链接组件：

```vue
<template>
  <!-- 基本用法 -->
  <NuxtLink to="/about">关于</NuxtLink>

  <!-- 使用路由对象 -->
  <NuxtLink :to="{ name: 'posts-id', params: { id: 1 } }">文章 1</NuxtLink>

  <!-- 外部链接（在新标签页打开） -->
  <NuxtLink to="https://nuxt.com" external>Nuxt</NuxtLink>

  <!-- 禁用预取 -->
  <NuxtLink to="/heavy-page" :prefetch="false">重型页面</NuxtLink>

  <!-- 替换历史记录而不是推送 -->
  <NuxtLink to="/page" replace>替换</NuxtLink>

  <!-- 自定义激活类 -->
  <NuxtLink
    to="/dashboard"
    active-class="text-primary"
    exact-active-class="font-bold"
  >
    仪表板
  </NuxtLink>
</template>
```

## NuxtPage

渲染当前页面组件（在布局中使用）：

```vue
<!-- app/app.vue -->
<template>
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>
```

带页面过渡：

```vue
<template>
  <NuxtPage :transition="{ name: 'fade', mode: 'out-in' }" />
</template>
```

向页面传递 props：

```vue
<template>
  <NuxtPage :page-key="route.fullPath" :foobar="123" />
</template>
```

## NuxtLayout

控制布局渲染：

```vue
<!-- app/app.vue -->
<template>
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>
```

动态布局：

```vue
<template>
  <NuxtLayout :name="layout">
    <NuxtPage />
  </NuxtLayout>
</template>

<script setup>
const layout = computed(() => isAdmin ? 'admin' : 'default')
</script>
```

带过渡的布局：

```vue
<template>
  <NuxtLayout :transition="{ name: 'slide', mode: 'out-in' }">
    <NuxtPage />
  </NuxtLayout>
</template>
```

## NuxtLoadingIndicator

页面导航的进度条：

```vue
<!-- app/app.vue -->
<template>
  <NuxtLoadingIndicator
    color="repeating-linear-gradient(to right, #00dc82 0%, #34cdfe 50%, #0047e1 100%)"
    :height="3"
    :duration="2000"
    :throttle="200"
  />
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>
```

## NuxtErrorBoundary

捕获和处理子组件中的错误：

```vue
<template>
  <NuxtErrorBoundary @error="handleError">
    <ComponentThatMightFail />

    <template #error="{ error, clearError }">
      <div class="error">
        <p>出错了: {{ error.message }}</p>
        <button @click="clearError">重试</button>
      </div>
    </template>
  </NuxtErrorBoundary>
</template>

<script setup>
function handleError(error) {
  console.error('捕获到错误:', error)
}
</script>
```

## ClientOnly

仅在客户端渲染内容：

```vue
<template>
  <ClientOnly>
    <!-- 仅浏览器组件 -->
    <BrowserOnlyChart :data="chartData" />

    <template #fallback>
      <p>加载图表中...</p>
    </template>
  </ClientOnly>
</template>
```

## DevOnly

仅在开发环境中渲染内容：

```vue
<template>
  <DevOnly>
    <DebugPanel />
  </DevOnly>
</template>
```

## NuxtIsland

服务器组件（实验性）：

```vue
<template>
  <NuxtIsland name="HeavyComponent" :props="{ data: complexData }" />
</template>
```

## NuxtImg 和 NuxtPicture

优化的图片（需要 `@nuxt/image` 模块）：

```vue
<template>
  <!-- 基本优化图片 -->
  <NuxtImg src="/images/hero.jpg" width="800" height="600" />

  <!-- 响应式带 srcset -->
  <NuxtImg
    src="/images/hero.jpg"
    sizes="sm:100vw md:50vw lg:400px"
    :modifiers="{ format: 'webp' }"
  />

  <!-- 使用 picture 的艺术指导 -->
  <NuxtPicture
    src="/images/hero.jpg"
    :img-attrs="{ alt: 'Hero 图片' }"
  />
</template>
```

## Teleport

在组件树外部渲染内容：

```vue
<template>
  <button @click="showModal = true">打开模态框</button>

  <Teleport to="body">
    <div v-if="showModal" class="modal">
      <p>模态框内容</p>
      <button @click="showModal = false">关闭</button>
    </div>
  </Teleport>
</template>
```

对于 SSR，将 `<ClientOnly>` 与 Teleport 一起使用：

```vue
<template>
  <ClientOnly>
    <Teleport to="#teleports">
      <Modal />
    </Teleport>
  </ClientOnly>
</template>
```

## NuxtRouteAnnouncer

无障碍功能：向屏幕阅读器宣布页面变化：

```vue
<!-- app/app.vue -->
<template>
  <NuxtRouteAnnouncer />
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>
```

## 性能优化

### 预取策略

```vue
<template>
  <!-- 自动预取链接 -->
  <NuxtLink to="/about">关于</NuxtLink>
  
  <!-- 禁用预取 -->
  <NuxtLink to="/heavy" :prefetch="false">重型页面</NuxtLink>
  
  <!-- 按需预取 -->
  <NuxtLink to="/data" prefetch-on="interaction">数据页</NuxtLink>
</template>
```

### 路由过渡

```vue
<template>
  <NuxtPage :transition="{ name: 'fade', mode: 'out-in' }" />
</template>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
```

## 无障碍功能

### 路由宣布

```vue
<template>
  <!-- 为屏幕阅读器宣布页面变化 -->
  <NuxtRouteAnnouncer />
  
  <!-- 自定义宣布 -->
  <NuxtRouteAnnouncer>
    <template #default="{ message }">
      <div aria-live="polite">{{ message }}</div>
    </template>
  </NuxtRouteAnnouncer>
</template>
```

## 错误处理

### 全局错误边界

```vue
<!-- app/app.vue -->
<template>
  <NuxtLayout>
    <NuxtErrorBoundary>
      <NuxtPage />
      
      <template #error="{ error, clearError }">
        <div class="error-page">
          <h1>出错了</h1>
          <p>{{ error.message }}</p>
          <button @click="clearError">重试</button>
        </div>
      </template>
    </NuxtErrorBoundary>
  </NuxtLayout>
</template>
```

## 最佳实践

1. **使用 NuxtLink** - 自动预取和优化
2. **懒加载重型组件** - 使用 Lazy 前缀
3. **错误边界** - 使用 NuxtErrorBoundary 处理错误
4. **SSR 安全** - 使用 ClientOnly 包裹浏览器专用组件
5. **无障碍** - 使用 NuxtRouteAnnouncer

## 常见问题

### 导航不工作

**原因：** 使用了 `<a>` 标签而不是 NuxtLink

**解决：**

```vue
<template>
  <!-- ❌ 错误 -->
  <a href="/about">关于</a>
  
  <!-- ✅ 正确 -->
  <NuxtLink to="/about">关于</NuxtLink>
</template>
```

### 水合不匹配

**原因：** 条件渲染不一致

**解决：**

```vue
<template>
  <ClientOnly>
    <BrowserComponent />
    <template #fallback>
      <p>Loading...</p>
    </template>
  </ClientOnly>
</template>
```

<!--
Source references:
- https://nuxt.com/docs/api/components/nuxt-link
- https://nuxt.com/docs/api/components/nuxt-page
- https://nuxt.com/docs/api/components/nuxt-layout
- https://nuxt.com/docs/api/components/client-only
-->
