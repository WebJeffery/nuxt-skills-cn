---
name: nuxt-performance
description: 专为 Nuxt.js 项目提供全面的性能优化最佳实践技能。
  帮助开发者提升应用加载速度、运行时性能和用户体验。
  涵盖代码分割、懒加载、缓存策略、图片优化、SSR/SSG 优化、Core Web Vitals 等内容。

  此技能应在以下情况使用:
  - 用户要求"Nuxt 性能优化"、"Nuxt performance"、"Web Vitals"
  - 网站加载速度慢
  - 需要优化 LCP、FID、CLS 等 Core Web Vitals 指标
  - 需要减少包体积和提升运行时性能
  - 需要配置缓存策略和 CDN
  - 需要优化 SEO 和首屏渲染

allowed-tools:
  - Read
  - Write
  - Edit
  - AskUserQuestion
metadata:
  trigger: Nuxt 性能优化、performance、Core Web Vitals、LCP、FID、CLS
  version: 1.0
---

# Nuxt Performance: Nuxt.js 性能优化最佳实践技能

## 技能目的

为 Nuxt.js 项目提供系统化的性能优化方案,从代码层面到架构层面全面提升应用性能,确保快速的用户体验和优秀的搜索引擎排名。

## 使用时机

### 触发关键词
- "Nuxt 性能优化"、"Nuxt performance"
- "Core Web Vitals"、"LCP"、"FID"、"CLS"
- "页面加载慢"、"首屏渲染"
- "代码分割"、"懒加载"
- "缓存策略"、"CDN 优化"
- "包体积优化"、"tree shaking"

### 项目类型
- 需要提升加载速度的 Nuxt 3/4 项目
- 用户体验评分低的项目
- 移动端性能差的应用
- 需要通过 Core Web Vitals 认证的项目

### 优化场景
- 首屏加载时间过长
- 包体积过大
- 运行时性能差
- 图片加载慢
- API 响应慢
- SEO 评分低

## 核心性能指标

### Core Web Vitals

**1. LCP (Largest Contentful Paint)** - 最大内容绘制
- **目标**: < 2.5 秒
- **衡量**: 页面主要内容加载完成的时间

**2. FID (First Input Delay)** - 首次输入延迟
- **目标**: < 100 毫秒
- **衡量**: 用户首次交互到浏览器响应的时间

**3. CLS (Cumulative Layout Shift)** - 累积布局偏移
- **目标**: < 0.1
- **衡量**: 页面布局稳定性的指标

### 其他关键指标

- **FCP (First Contentful Paint)**: < 1.8 秒
- **TTI (Time to Interactive)**: < 3.8 秒
- **TBT (Total Blocking Time)**: < 200 毫秒
- **SI (Speed Index)**: < 3.4 秒

## 性能检测工具

### 1. Chrome DevTools

```bash
# 快捷键: F12
# 查看:
# - Performance 面板: 运行时性能分析
# - Lighthouse: 综合性能审计
# - Network: 网络请求分析
```

### 2. Lighthouse

```bash
# 命令行
npx lighthouse https://example.com --view

# 或在 Chrome DevTools 中运行
# Lighthouse > 选择 "Performance" > "分析页面加载"
```

### 3. WebPageTest

```bash
# 访问 https://www.webpagetest.org
# 输入 URL 并测试
```

### 4. Nuxt DevTools

```bash
# 安装
npm install --save-dev @nuxt/devtools

# 访问 http://localhost:3000/__nuxt
# 查看:
# - 组件渲染性能
# - 路由加载时间
# - 包体积分析
```

## 性能优化策略

### Step 1: 渲染模式优化

#### 选择合适的渲染模式

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  // 静态站点生成 (最快)
  ssr: true,
  nitro: {
    preset: 'static', // 或 'vercel-static'
  },

  // 或使用混合渲染
  routeRules: {
    // 首页: SSG
    '/': { isr: true },
    // 博客文章: ISR (每小时更新)
    '/blog/**': { isr: 3600 },
    // 管理后台: SSR
    '/admin/**': { ssr: true },
    // API: SWR 或缓存
    '/api/**': { cache: { maxAge: 60 * 60 } },
  },
})
```

**性能对比:**
- **SSG**: 最快,无服务器渲染开销
- **ISR**: 接近 SSG,支持增量更新
- **SSR**: 较快,SEO 友好
- **CSR**: 最慢,不推荐

### Step 2: 代码分割与懒加载

#### 路由级代码分割

```typescript
// 自动路由分割 (Nuxt 默认)
// pages/index.vue, pages/about.vue 自动分割

// 手动路由分割
const AboutPage = defineAsyncComponent(() =>
  import('~/pages/about.vue')
)
```

#### 组件懒加载

```vue
<template>
  <!-- 懒加载组件 -->
  <LazyHomePageHero v-if="showHero" />

  <!-- 仅在需要时加载 -->
  <button @click="showModal = true">打开模态框</button>
  <LazyModal v-if="showModal" @close="showModal = false" />
</template>

<script setup lang="ts">
// 自动导入 Lazy 前缀组件
const showModal = ref(false)
</script>
```

#### 动态导入

```typescript
// 按需加载库
const formatCurrency = (amount: number) => {
  return import('~/utils/currency')
    .then(m => m.default(amount))
}

// 条件导入
if (process.client) {
  const { default: ChartJS } = await import('chart.js')
}
```

### Step 3: 图片优化

#### 使用 Nuxt Image

```bash
npm install @nuxt/image
```

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@nuxt/image'],

  image: {
    // 优化选项
    formats: ['webp', 'avif'],
    quality: 80,
    domains: ['cdn.example.com'],

    // 响应式图片
    screens: {
      'xs': 320,
      'sm': 640,
      'md': 768,
      'lg': 1024,
      'xl': 1280,
    },
  },
})
```

```vue
<template>
  <!-- 基础用法 -->
  <NuxtImg
    src="/hero.jpg"
    alt="Hero"
    width="800"
    height="600"
    loading="lazy"
  />

  <!-- 响应式图片 -->
  <NuxtImg
    src="/hero.jpg"
    sizes="sm:100vw md:50vw lg:400px"
    modifiers={{ format: 'webp', quality: 80 }}
  />

  <!-- 预加载关键图片 -->
  <NuxtImg
    src="/logo.png"
    priority
  />

  <!-- 使用图片 CDN -->
  <NuxtImg
    provider="cloudinary"
    src="/uploads/hero.jpg"
    width="800"
    height="600"
  />
</template>
```

#### 原生图片优化

```vue
<template>
  <!-- 懒加载 -->
  <img
    src="/photo.jpg"
    alt="描述"
    loading="lazy"
    decoding="async"
    width="800"
    height="600"
  />

  <!-- 预加载关键图片 -->
  <link rel="preload" as="image" href="/hero.jpg" />
</template>
```

### Step 4: 缓存策略

#### HTTP 缓存

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    // 公共资源: 长期缓存
    '/api/articles': { cache: { maxAge: 60 * 60 * 24 } }, // 1 天
    '/static/**': { headers: { 'Cache-Control': 'public, max-age=31536000' } },

    // API 响应: 短期缓存
    '/api/**': { cache: { maxAge: 60 } }, // 1 分钟
  },

  nitro: {
    routeRules: {
      '/api/feed': { cache: { maxAge: 60 * 60 } },
    },
  },
})
```

#### 组件缓存

```vue
<template>
  <!-- 使用 keep-alive -->
  <KeepAlive>
    <component :is="currentComponent" />
  </KeepAlive>
</template>
```

#### 数据缓存

```typescript
// 使用 useAsyncData 缓存
const { data } = await useAsyncData('articles', () =>
  $fetch('/api/articles'),
  {
    // 5 分钟缓存
    transform: (data) => data.articles,
    getCachedData: (key) => useNuxtData(key).data,
  }
)

// 使用 useFetch 缓存
const { data, refresh } = await useFetch('/api/user', {
  // 1 分钟缓存
  key: 'user',
  getCachedData: (key) => useNuxtData(key).data,
})
```

### Step 5: 包体积优化

#### Tree Shaking

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  build: {
    transpile: ['@nuxtjs/tailwindcss'],
  },

  vite: {
    build: {
      rollupOptions: {
        output: {
          manualChunks: {
            // 将大型库分离到单独的 chunk
            'vendor-vue': ['vue', 'vue-router'],
            'vendor-ui': ['@nuxtjs/color-mode'],
          },
        },
      },
    },
  },
})
```

#### 按需导入

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  // 自动导入优化
  imports: {
    dirs: ['~/composables'],
  },
})
```

```typescript
// ❌ 导入整个库
import { cloneDeep, sortBy } from 'lodash'

// ✅ 按需导入
import cloneDeep from 'lodash/cloneDeep'
import sortBy from 'lodash/sortBy'

// 或使用 unlodash
import { cloneDeep, sortBy } from 'lodash-es'
```

#### 移除未使用的代码

```bash
# 分析包体积
npm run build -- --analyze

# 使用 nuxi analyze
npx nuxi analyze
```

### Step 6: CSS 优化

#### CSS 代码分割

```vue
<template>
  <div :class="$style.container">
    <!-- Scoped CSS 自动分割 -->
  </div>
</template>

<style scoped>
.container {
  /* 只在当前组件生效 */
}
</style>
```

#### CSS 压缩与优化

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  css: ['~/assets/css/main.css'],

  vite: {
    css: {
      postcss: {
        plugins: [
          // CSS 压缩
          require('cssnano')({
            preset: 'default',
          }),
        ],
      },
    },
  },
})
```

#### 关键 CSS 内联

```vue
<template>
  <!-- 首屏关键 CSS 内联 -->
  <style>
  /* 关键渲染路径 CSS */
  .hero { min-height: 100vh; }
  </style>
</template>
```

### Step 7: JavaScript 优化

#### 减少主线程阻塞

```typescript
// 使用 Web Workers
const worker = new Worker('~/workers/heavy-task.worker.ts', {
  type: 'module'
})

worker.postMessage({ data: largeDataSet })
worker.onmessage = (e) => {
  console.log('计算完成:', e.data)
}
```

#### 代码拆分

```typescript
// 延迟执行非关键 JS
onMounted(() => {
  setTimeout(() => {
    import('~/analytics').then(({ init }) => init())
  }, 2000)
})
```

#### 防抖与节流

```typescript
// composables/useDebounce.ts
export function useDebounce<T>(fn: (...args: T[]) => any, delay: number) {
  let timeout: ReturnType<typeof setTimeout>

  return (...args: T[]) => {
    clearTimeout(timeout)
    timeout = setTimeout(() => fn(...args), delay)
  }
}

// 使用
const { fetchSuggestions } = useDebounce(async (query: string) => {
  suggestions.value = await $fetch('/api/suggest', { q: query })
}, 300)
```

### Step 8: 预加载与预连接

```vue
<template>
  <head>
    <!-- 预连接到第三方域名 -->
    <link rel="preconnect" href="https://cdn.example.com" />
    <link rel="dns-prefetch" href="https://api.example.com" />

    <!-- 预加载关键资源 -->
    <link rel="preload" as="style" href="/fonts/main.woff2" />
    <link rel="preload" as="script" href="/critical.js" />

    <!-- 预获取下一页资源 -->
    <link rel="prefetch" href="/about" />
  </head>
</template>
```

### Step 9: API 优化

#### 数据缓存

```typescript
// server/api/articles.ts
export default defineEventHandler(async (event) => {
  // 使用 Nitro 缓存
  const articles = await storage.getItem('articles')

  if (articles) {
    return articles
  }

  const data = await fetchArticles()
  await storage.setItem('articles', data, { ttl: 3600 }) // 1 小时

  return data
})
```

#### 响应压缩

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    compressPublicAssets: true, // 启用 gzip/brotli 压缩
  },
})
```

#### 分页与懒加载

```typescript
// composables/useInfiniteScroll.ts
export function useInfiniteScroll() {
  const articles = ref([])
  const page = ref(1)
  const loading = ref(false)

  const loadMore = async () => {
    if (loading.value) return

    loading.value = true
    const data = await $fetch('/api/articles', {
      params: { page: page.value }
    })

    articles.value.push(...data)
    page.value++
    loading.value = false
  }

  return { articles, loadMore, loading }
}
```

### Step 10: 监控与分析

#### 性能监控

```typescript
// plugins/analytics.client.ts
export default defineNuxtPlugin((nuxtApp) => {
  if (process.env.NODE_ENV === 'production') {
    // 监控 Web Vitals
    import('web-vitals').then(({ getCLS, getFID, getFCP, getLCP, getTTFB }) => {
      getCLS(console.log)
      getFID(console.log)
      getFCP(console.log)
      getLCP(console.log)
      getTTFB(console.log)
    })
  }
})
```

#### 错误监控

```typescript
// plugins/sentry.client.ts
import * as Sentry from '@sentry/vue'

export default defineNuxtPlugin((nuxtApp) => {
  Sentry.init({
    dsn: 'your-dsn',
    environment: process.env.NODE_ENV,

    // 性能监控
    integrations: [
      new Sentry.BrowserTracing({
        routingInstrumentation: Sentry.vueRouterInstrumentation(nuxtApp.$router),
      }),
    ],

    tracesSampleRate: 0.1, // 10% 的请求被追踪
  })
})
```

## 性能检查清单

### 关键指标
- [ ] LCP < 2.5s
- [ ] FID < 100ms
- [ ] CLS < 0.1
- [ ] FCP < 1.8s
- [ ] TTI < 3.8s

### 代码优化
- [ ] 启用代码分割
- [ ] 组件懒加载
- [ ] 图片优化(WebP/AVIF)
- [ ] CSS 代码分割
- [ ] 移除未使用的依赖

### 缓存策略
- [ ] HTTP 缓存配置
- [ ] API 响应缓存
- [ ] 静态资源 CDN
- [ ] Service Worker

### 渲染优化
- [ ] 使用 SSG/ISR
- [ ] 预渲染关键页面
- [ ] 骨架屏
- [ ] 避免布局偏移

## 输出格式

**优化报告示例:**

```markdown
# 性能优化报告

## 优化前
- LCP: 4.2s
- FID: 180ms
- CLS: 0.25
- Performance: 45

## 优化后
- LCP: 1.8s ✅
- FID: 65ms ✅
- CLS: 0.05 ✅
- Performance: 92 ✅

## 优化措施
1. 启用 SSG 渲染
2. 图片优化(WebP,懒加载)
3. 代码分割
4. HTTP 缓存策略
```

## 参考资源

在需要更详细指导时,加载以下参考文件:

**核心优化**
- `references/rendering-modes.md` - 渲染模式选择指南 (SSG/ISR/SSR/CSR)
- `references/code-splitting.md` - 代码分割与懒加载
- `references/caching-strategies.md` - 缓存策略配置

**资源优化**
- `references/image-optimization.md` - 图片优化完整指南
- `references/video-optimization.md` - 视频优化完整指南
- `references/font-optimization.md` - 字体优化完整指南
- `references/css-optimization.md` - CSS 优化完整指南

**高级优化**
- `references/bundle-analysis.md` - 包体积分析与优化
- `references/core-web-vitals.md` - Core Web Vitals 详解
- `references/third-party-scripts.md` - 第三方脚本加载优化

## 注意事项

1. **渐进增强** - 先确保功能可用,再优化性能
2. **真实测试** - 在真实设备和网络条件下测试
3. **移动优先** - 优先优化移动端性能
4. **持续监控** - 建立性能监控系统
5. **权衡取舍** - 性能与功能的平衡
6. **定期审计** - 每季度进行性能审计

## 常见错误

### ❌ 错误 1: 过早优化

```typescript
// ❌ 在不知道瓶颈的情况下优化
// 先分析,再优化

// ✅ 正确流程
// 1. 使用 Lighthouse 分析
// 2. 识别性能瓶颈
// 3. 针对性优化
// 4. 验证优化效果
```

### ❌ 错误 2: 忽略移动端

```vue
<!-- ❌ 只在桌面环境优化 -->
<img src="/large-image.jpg" />

<!-- ✅ 响应式优化 -->
<picture>
  <source srcset="/small.webp" media="(max-width: 768px)" />
  <source srcset="/large.webp" media="(min-width: 769px)" />
  <img src="/large.jpg" alt="描述" />
</picture>
```

### ❌ 错误 3: 过度缓存

```typescript
// ❌ 缓存所有内容
cache: { maxAge: 31536000 }

// ✅ 合理的缓存策略
cache: {
  maxAge: 60 * 60, // 1 小时
  varies: ['Authorization', 'Cookie']
}
```

### ❌ 错误 4: 忽略 CLS

```vue
<!-- ❌ 导致布局偏移 -->
<div v-if="loaded">{{ content }}</div>
<div v-else>加载中...</div>

<!-- ✅ 预留空间 -->
<div class="min-h-[200px]">
  <div v-if="loaded">{{ content }}</div>
  <div v-else>加载中...</div>
</div>
```
