---
name: rendering-modes
description: 通用渲染、客户端渲染和 Nuxt 中的混合渲染
---

# 渲染模式

Nuxt 支持多种渲染模式：通用 (SSR)、客户端 (CSR) 和混合渲染。

## 通用渲染（默认）

服务器渲染 HTML，然后在客户端水合：

```ts
// nuxt.config.ts - 这是默认设置
export default defineNuxtConfig({
  ssr: true,
})
```

**优点：**
- 快速的初始页面加载（HTML 已就绪）
- SEO 友好（内容在 HTML 中）
- 无需 JavaScript 也能正常工作

**工作原理：**
1. 服务器执行 Vue 代码，生成 HTML
2. 浏览器立即显示 HTML
3. JavaScript 加载并水合页面
4. 页面变为完全交互式

## 客户端渲染

完全在浏览器中渲染：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  ssr: false,
})
```

**优点：**
- 开发更简单（无 SSR 约束）
- 更便宜的托管（仅静态文件）
- 离线工作

**使用场景：**
- 管理员仪表板
- SaaS 应用
- 认证后面的应用

### SPA 加载模板

应用水合时提供加载 UI：

```html
<!-- app/spa-loading-template.html -->
<div class="loading">
  <div class="spinner"></div>
  <p>Loading...</p>
</div>

<style>
.loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
}
.spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #f3f3f3;
  border-top: 4px solid #00dc82;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}
@keyframes spin {
  to { transform: rotate(360deg); }
}
</style>
```

## 混合渲染

使用路由规则在每个路由混合渲染模式：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    // 静态页面 - 构建时预渲染
    '/': { prerender: true },
    '/about': { prerender: true },

    // ISR - 后台重新生成
    '/blog/**': { isr: 3600 }, // 缓存 1 小时
    '/products/**': { swr: true }, // 过时重新验证
    
    // 仅客户端渲染
    '/admin/**': { ssr: false },
    '/dashboard/**': { ssr: false },

    // 服务器渲染（默认）
    '/api/**': { cors: true },
  },
})
```

### 路由规则参考

| 规则 | 描述 |
|------|-------------|
| `prerender: true` | 构建时预渲染 |
| `ssr: false` | 仅客户端 |
| `swr: number \| true` | 过时重新验证缓存 |
| `isr: number \| true` | 增量静态重新生成 |
| `cache: { maxAge: number }` | 带有 TTL 的缓存 |
| `redirect: string` | 重定向到另一个路径 |
| `cors: true` | 添加 CORS 头 |
| `headers: object` | 自定义响应头 |

### 每页路由规则

```vue
<script setup lang="ts">
defineRouteRules({
  prerender: true,
})
</script>
```

## 预渲染

在构建设置生成静态 HTML：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  // 预渲染特定路由
  routeRules: {
    '/': { prerender: true },
    '/about': { prerender: true },
    '/posts/*': { prerender: true },
  },
})
```

或使用 `nuxt generate`：

```bash
nuxt generate
```

### 程序化预渲染

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  hooks: {
    'prerender:routes': ({ routes }) => {
      // 添加动态路由
      const posts = await fetchPostSlugs()
      for (const slug of posts) {
        routes.add(`/posts/${slug}`)
      }
    },
  },
})
```

或在页面中：

```ts
// server/api/posts.ts 或插件
prerenderRoutes(['/posts/1', '/posts/2', '/posts/3'])
```

## 边缘渲染

在 CDN 边缘服务器渲染：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    preset: 'cloudflare-pages', // 或 'vercel-edge', 'netlify-edge'
  },
})
```

支持的平台：
- Cloudflare Pages/Workers
- Vercel Edge Functions
- Netlify Edge Functions

## 条件渲染

使用 `import.meta.server` 和 `import.meta.client`：

```vue
<script setup>
if (import.meta.server) {
  // 服务器专用代码
  console.log('在服务器运行')
}

if (import.meta.client) {
  // 客户端专用代码
  console.log('在浏览器运行')
}
</script>
```

对于组件：

```vue
<template>
  <ClientOnly>
    <BrowserOnlyComponent />
    <template #fallback>
      <p>Loading...</p>
    </template>
  </ClientOnly>
</template>
```

## 性能优化

### 选择合适的渲染模式

```ts
// 根据内容特性选择
export default defineNuxtConfig({
  routeRules: {
    // 静态内容 - 预渲染
    '/': { prerender: true },
    '/about': { prerender: true },
    
    // 动态但不频繁变化 - ISR
    '/blog/**': { isr: 3600 },
    '/docs/**': { isr: 86400 },
    
    // 高度动态 - CSR
    '/dashboard/**': { ssr: false },
    '/admin/**': { ssr: false },
  },
})
```

### 预取策略

```vue
<template>
  <!-- 智能预取 -->
  <NuxtLink 
    to="/important-page" 
    prefetch 
    prefetch-on="interaction"
  >
    重要页面
  </NuxtLink>
</template>
```

## 高级用法

### 动态路由规则

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    '/products/*': {
      // 根据产品类型选择渲染模式
      isr: ({ url }) => {
        if (url.includes('featured')) {
          return 300 // 5 分钟
        }
        return 3600 // 1 小时
      },
    },
  },
})
```

### 自定义预渲染

```ts
// server/routes/prerender.ts
export default defineEventHandler(async (event) => {
  const posts = await $fetch('/api/posts')
  
  const routes = posts.map((post: Post) => ({
    route: `/posts/${post.slug}`,
    headers: { 'Cache-Control': 'public, max-age=3600' },
  }))
  
  return routes
})
```

### 条件渲染模式

```vue
<script setup>
const route = useRoute()

const renderingMode = computed(() => {
  if (route.path.startsWith('/admin')) return 'CSR'
  if (route.path.startsWith('/blog')) return 'ISR'
  return 'SSR'
})
</script>

<template>
  <div>
    <p>渲染模式：{{ renderingMode }}</p>
  </div>
</template>
```

## SEO 优化

### 元数据管理

```vue
<script setup lang="ts">
const route = useRoute()

useSeoMeta({
  title: '我的网站',
  description: '网站描述',
  ogTitle: 'Open Graph 标题',
  ogDescription: 'Open Graph 描述',
  ogImage: '/og-image.png',
  twitterCard: 'summary_large_image',
})
</script>
```

### 结构化数据

```vue
<script setup lang="ts">
useSchemaOrg([
  {
    '@context': 'https://schema.org',
    '@type': 'Article',
    headline: '文章标题',
    image: '/image.png',
    datePublished: '2024-01-01',
  },
])
</script>
```

## 监控和调试

### 渲染模式检测

```ts
// composables/useRenderingMode.ts
export function useRenderingMode() {
  const route = useRoute()
  const config = useRuntimeConfig()
  
  return computed(() => {
    const rules = config.routeRules || {}
    const path = route.path
    
    for (const pattern in rules) {
      if (path.match(pattern)) {
        return rules[pattern]
      }
    }
    
    return config.ssr ? 'SSR' : 'CSR'
  })
}
```

### 性能监控

```vue
<script setup>
onMounted(() => {
  const perfData = performance.getEntriesByType('navigation')[0] as PerformanceNavigationTiming
  
  console.log('DOM 加载时间：', perfData.domContentLoadedEventEnd)
  console.log('完全加载时间：', perfData.loadEventEnd)
})
</script>
```

## 最佳实践

1. **混合使用** - 根据内容特性选择渲染模式
2. **SEO 优先** - 公共内容使用 SSR/ISR
3. **用户体验** - 管理后台使用 CSR
4. **缓存策略** - 合理设置缓存时间
5. **性能监控** - 跟踪渲染性能

## 常见问题

### ISR 缓存不更新

**解决：**

```ts
// 手动触发缓存更新
const { refreshNuxtData } = useNuxtData()

await refreshNuxtData('blog-posts')
```

### 水合错误

**解决：**

```vue
<template>
  <!-- 使用 ClientOnly 包裹问题组件 -->
  <ClientOnly>
    <BrowserSpecificComponent />
    <template #fallback>
      <p>Loading...</p>
    </template>
  </ClientOnly>
</template>
```

### 预渲染路由不完整

**解决：**

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  hooks: {
    'nitro:init': async (nitro) => {
      // 添加动态路由到预渲染列表
      const routes = await fetchDynamicRoutes()
      for (const route of routes) {
        nitro.options.prerender.routes.push(route)
      }
    },
  },
})
```

<!-- 
源码参考：
- https://nuxt.com/docs/guide/concepts/rendering
- https://nuxt.com/docs/getting-started/prerendering
- https://nuxt.com/docs/api/nuxt-config#routerules
-->