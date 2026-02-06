# Core Web Vitals 完整指南

## Core Web Vitals 概述

Core Web Vitals 是 Google 衡量用户体验的核心指标,直接影响搜索排名。

**三大核心指标:**
1. **LCP** (Largest Contentful Paint) - 加载性能
2. **FID** (First Input Delay) - 交互性
3. **CLS** (Cumulative Layout Shift) - 视觉稳定性

## 1. LCP (Largest Contentful Paint)

### 定义

页面主要内容加载完成的时间。

**目标:**
- ✅ 良好: < 2.5 秒
- ⚠️ 需改进: 2.5 - 4.0 秒
- ❌ 差: > 4.0 秒

### 影响 LCP 的因素

1. **服务器响应慢**
2. **JavaScript/CSS 阻塞渲染**
3. **资源加载慢**
4. **客户端渲染**

### 优化 LCP

#### 1. 使用 SSG/ISR

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  ssr: true,
  nitro: {
    preset: 'static', // 或 'vercel-static'
  },

  routeRules: {
    '/': { isr: true },
    '/blog/**': { isr: 3600 },
  },
})
```

#### 2. 预加载关键资源

```vue
<template>
  <head>
    <!-- 预加载关键 CSS -->
    <link rel="preload" as="style" href="/styles/critical.css" />

    <!-- 预加载关键字体 -->
    <link rel="preload" as="font" href="/fonts/main.woff2" crossorigin />

    <!-- 预加载 LCP 图片 -->
    <link rel="preload" as="image" href="/hero.webp" />
  </head>
</template>
```

#### 3. 优化 LCP 图片

```vue
<template>
  <!-- 使用 Nuxt Image -->
  <NuxtImg
    src="/hero.jpg"
    width="1200"
    height="800"
    format="webp"
    priority
    alt="Hero"
  />

  <!-- 或使用原生 -->
  <img
    src="/hero.webp"
    width="1200"
    height="800"
    loading="eager"
    decoding="sync"
    alt="Hero"
  />
</template>
```

#### 4. 内联关键 CSS

```vue
<template>
  <style>
  /* 关键渲染路径 CSS */
  .hero { min-height: 100vh; display: flex; }
  .hero-content { flex: 1; padding: 2rem; }
  </style>
</template>
```

#### 5. 压缩资源

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    compressPublicAssets: true, // gzip/brotli
  },

  vite: {
    build: {
      minify: 'terser',
      cssCodeSplit: true,
    },
  },
})
```

#### 6. 使用 CDN

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  app: {
    cdnURL: 'https://cdn.example.com',
  },

  image: {
    domains: ['cdn.example.com'],
  },
})
```

## 2. FID (First Input Delay)

### 定义

用户首次交互到浏览器响应的时间。

**目标:**
- ✅ 良好: < 100 毫秒
- ⚠️ 需改进: 100 - 300 毫秒
- ❌ 差: > 300 毫秒

**注意:** FID 将被 INP (Interaction to Next Paint) 替代。

### 影响 FID 的因素

1. **大量 JavaScript 执行**
2. **长任务阻塞主线程**
3. **同步脚本**

### 优化 FID

#### 1. 减少JavaScript执行时间

```vue
<script setup lang="ts">
// ❌ 阻塞主线程
const heavyTask = () => {
  // 大量计算...
}

// ✅ 使用 Web Worker
const worker = new Worker('~/workers/heavy-task.worker.ts')
worker.postMessage(data)
worker.onmessage = (e) => {
  result.value = e.data
}
</script>
```

#### 2. 代码分割

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  vite: {
    build: {
      rollupOptions: {
        output: {
          manualChunks: {
            'vendor': ['vue', 'vue-router'],
            'ui': ['@nuxtjs/color-mode'],
          },
        },
      },
    },
  },
})
```

#### 3. 延迟非关键 JS

```typescript
// plugins/analytics.client.ts
export default defineNuxtPlugin((nuxtApp) => {
  // 延迟加载分析脚本
  setTimeout(() => {
    import('~/analytics').then(({ init }) => init())
  }, 2000)
})
```

#### 4. 使用 defer/async

```vue
<template>
  <head>
    <!-- 非阻塞加载 -->
    <script src="/analytics.js" defer />
    <script src="/chat-widget.js" async />
  </head>
</template>
```

#### 5. 避免长任务

```typescript
// 分割长任务
function runTasks(tasks: Task[]) {
  let i = 0

  function runChunk() {
    const start = performance.now()

    while (i < tasks.length && performance.now() - start < 50) {
      tasks[i]()
      i++
    }

    if (i < tasks.length) {
      requestIdleCallback(runChunk)
    }
  }

  runChunk()
}
```

## 3. CLS (Cumulative Layout Shift)

### 定义

页面布局在加载过程中的偏移量。

**目标:**
- ✅ 良好: < 0.1
- ⚠️ 需改进: 0.1 - 0.25
- ❌ 差: > 0.25

### 影响 CLS 的因素

1. **图片没有尺寸**
2. **字体加载**
3. **动态插入内容**
4. **异步广告**

### 优化 CLS

#### 1. 为图片设置宽高

```vue
<template>
  <!-- ✅ 设置宽高 -->
  <NuxtImg
    src="/photo.jpg"
    width="800"
    height="600"
    alt="Photo"
  />

  <!-- ❌ 缺少尺寸 -->
  <img src="/photo.jpg" alt="Photo" />
</template>
```

#### 2. 为广告预留空间

```vue
<template>
  <div class="ad-container" style="min-height: 250px;">
    <广告位 v-if="adLoaded" />
  </div>
</template>
```

#### 3. 字体优化

```css
/* 使用 font-display */
@font-face {
  font-family: 'Custom Font';
  src: url('/font.woff2') format('woff2');
  font-display: swap; /* 或 optional, fallback */
}
```

```vue
<template>
  <head>
    <!-- 预加载字体 -->
    <link rel="preload" as="font" href="/main.woff2" crossorigin />
  </head>
</template>
```

#### 4. 避免内容注入

```vue
<template>
  <!-- ❌ 导致布局偏移 -->
  <div v-if="loaded">{{ content }}</div>
  <div v-else>加载中...</div>

  <!-- ✅ 预留空间 -->
  <div class="min-h-[200px]">
    <div v-if="loaded">{{ content }}</div>
    <div v-else>加载中...</div>
  </div>
</template>
```

#### 5. 使用 CSS aspect-ratio

```vue
<template>
  <div class="image-container">
    <img src="/photo.jpg" alt="Photo" />
  </div>
</template>

<style scoped>
.image-container {
  aspect-ratio: 16 / 9;
  width: 100%;
}
</style>
```

## 监控 Core Web Vitals

### 1. 使用 web-vitals 库

```bash
npm install web-vitals
```

```typescript
// plugins/web-vitals.client.ts
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals'

export default defineNuxtPlugin(() => {
  if (process.env.NODE_ENV === 'production') {
    const sendToAnalytics = (metric: any) => {
      // 发送到分析服务
      $fetch('/api/analytics', {
        method: 'POST',
        body: {
          name: metric.name,
          value: metric.value,
          id: metric.id,
        },
      })
    }

    getCLS(sendToAnalytics)
    getFID(sendToAnalytics)
    getFCP(sendToAnalytics)
    getLCP(sendToAnalytics)
    getTTFB(sendToAnalytics)
  }
})
```

### 2. Google Search Console

```
1. 访问 https://search.google.com/search-console
2. 添加网站
3. 查看 "Core Web Vitals" 报告
```

### 3. PageSpeed Insights

```bash
# 命令行
npx lighthouse https://example.com --view

# 或访问
https://pagespeed.web.dev/
```

### 4. Chrome DevTools

```javascript
// 在控制台运行
new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    console.log(entry)
  }
}).observe({ entryTypes: ['largest-contentful-paint'] })
```

## 新指标: INP

**INP** (Interaction to Next Paint) 将替代 FID。

**目标:**
- ✅ 良好: < 200 毫秒
- ⚠️ 需改进: 200 - 500 毫秒
- ❌ 差: > 500 毫秒

```typescript
import { getINP } from 'web-vitals'

getINP((metric) => {
  console.log('INP:', metric.value)
})
```

## 实际优化案例

### 案例 1: 博客文章页

```vue
<template>
  <article>
    <!-- LCP: 预加载首图 -->
    <NuxtImg
      :src="article.image"
      width="1200"
      height="630"
      priority
      format="webp"
      :alt="article.title"
    />

    <!-- CLS: 避免布局偏移 -->
    <div class="content min-h-[500px]">
      <h1>{{ article.title }}</h1>
      <p>{{ article.content }}</p>
    </div>

    <!-- FID: 延迟加载评论 -->
    <LazyComments v-if="showComments" :article-id="article.id" />
  </article>
</template>

<script setup lang="ts">
const { data: article } = await useAsyncData(
  `article-${route.params.id}`,
  () => $fetch(`/api/articles/${route.params.id}`)
)

const showComments = ref(false)

// 延迟加载评论
onMounted(() => {
  setTimeout(() => {
    showComments.value = true
  }, 1000)
})
</script>
```

### 案例 2: 电商产品页

```vue
<template>
  <div class="product-page">
    <!-- LCP: 产品主图 -->
    <NuxtImg
      :src="product.image"
      width="800"
      height="800"
      priority
      format="webp"
      :alt="product.name"
    />

    <!-- CLS: 预留空间 -->
    <div class="product-info" style="min-height: 400px;">
      <h1>{{ product.name }}</h1>
      <p class="price">{{ product.price }}</p>

      <!-- FID: 延迟加载推荐 -->
      <LazyRecommendations v-if="showRecommendations" />
    </div>
  </div>
</template>
```

## 最佳实践

### ✅ LCP 优化

```markdown
- [ ] 使用 SSG/ISR
- [ ] 预加载关键资源
- [ ] 优化 LCP 图片
- [ ] 内联关键 CSS
- [ ] 使用 CDN
- [ ] 压缩资源
```

### ✅ FID 优化

```markdown
- [ ] 减少 JS 执行时间
- [ ] 代码分割
- [ ] 延迟非关键 JS
- [ ] 使用 Web Workers
- [ ] 避免长任务
```

### ✅ CLS 优化

```markdown
- [ ] 图片设置宽高
- [ ] 字体优化
- [ ] 预留空间
- [ ] 避免内容注入
- [ ] 使用 aspect-ratio
```

## 检查清单

```markdown
## Core Web Vitals 检查清单

### LCP (< 2.5s)
- [ ] 使用 SSR/SSG
- [ ] 预加载关键资源
- [ ] 优化 LCP 元素
- [ ] 减少服务器响应时间
- [ ] 启用 CDN
- [ ] 压缩资源

### FID (< 100ms)
- [ ] 减少 JS 大小
- [ ] 代码分割
- [ ] 延迟非关键 JS
- [ ] 减少主线程工作
- [ ] 使用 Web Workers

### CLS (< 0.1)
- [ ] 图片设置尺寸
- [ ] 字体优化
- [ ] 预留空间
- [ ] 避免动态内容
- [ ] 使用 CSS 占位

### 监控
- [ ] web-vitals 集成
- [ ] Google Search Console
- [ ] PageSpeed Insights
- [ ] 定期审计
```

## 参考资源

- [Core Web Vitals](https://web.dev/vitals/)
- [Optimizing LCP](https://web.dev/optimize-lcp/)
- [Optimizing FID](https://web.dev/optimize-fid/)
- [Optimizing CLS](https://web.dev/optimize-cls/)
- [web-vitals library](https://github.com/GoogleChrome/web-vitals)
