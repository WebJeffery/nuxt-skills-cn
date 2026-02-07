---
title: 对页面加载关键应用程序使用 SSR 或 SSG
impact: HIGH
impactDescription: 客户端 SPA 需要在内容出现之前执行 JavaScript，严重影响 LCP 和 INP 指标
type: capability
tags: [vue3, performance, ssr, ssg, nuxt, page-load, architecture]
---

# 对页面加载关键应用程序使用 SSR 或 SSG

**影响: 高** - 纯客户端 SPA 必须在用户看到内容之前下载、解析和执行 JavaScript。这会显著延迟最大内容绘制（LCP）并影响核心 Web 指标。对于页面加载关键的应用程序（营销网站、电子商务、内容网站），使用服务器端渲染（SSR）或静态站点生成（SSG）。

根据内容的性质和页面加载要求选择你的架构。

## 任务清单

- [ ] 评估页面加载性能是否对你的用例至关重要
- [ ] 对每个请求更改的动态内容选择 SSR
- [ ] 对不经常更改的内容选择 SSG
- [ ] 考虑混合方法（SSG 用于营销，SPA 用于应用）
- [ ] 使用 Nuxt.js 简化 Vue 的 SSR/SSG

## 架构决策指南

| 内容类型 | 更改 | 最佳方法 | 示例 |
|--------------|---------|---------------|---------|
| 营销页面 | 很少 | SSG | 着陆页、文档 |
| 博客/内容 | 发布时 | SSG 并重新生成 | 博客、文档 |
| 电子商务目录 | 每小时/每天 | SSG + ISR | 产品列表 |
| 用户仪表板 | 每个请求 | SPA（可以）| 管理面板 |
| 社交信息流 | 实时 | SSR 或 SPA | 新闻信息流 |
| 认证应用 | 每个用户 | SPA（可以）| 内部工具 |

**错误：**
```javascript
// 错误：营销网站的纯客户端 SPA
// 用户在 JS 加载和执行之前看到空白页面

// main.js
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')  // 在此运行之前没有任何可见内容

// index.html - 用户在 JS 执行之前看到空的 #app
// <div id="app"></div>
```

**正确：**
```javascript
// 正确：使用 Nuxt.js 进行 SSR/SSG
// nuxt.config.ts
export default defineNuxtConfig({
  // SSG：在构建时预渲染（最适合静态内容）
  ssr: true,
  nitro: {
    prerender: {
      routes: ['/', '/about', '/pricing', '/blog']
    }
  }
})

// 或者对动态内容进行完整 SSR
export default defineNuxtConfig({
  ssr: true  // 服务器在每个请求时渲染
})
```

```vue
<!-- pages/index.vue - 适用于 SSR 和 SSG -->
<template>
  <div>
    <HeroSection />
    <FeatureList :features="features" />
    <PricingTable />
  </div>
</template>

<script setup>
// 在构建时（SSG）或请求时（SSR）获取数据
const { data: features } = await useFetch('/api/features')
</script>
```

## 混合方法：SSG 营销 + SPA 应用

```javascript
// nuxt.config.ts - 混合渲染
export default defineNuxtConfig({
  routeRules: {
    // 静态页面 - 在构建时预渲染
    '/': { prerender: true },
    '/about': { prerender: true },
    '/pricing': { prerender: true },
    '/blog/**': { prerender: true },

    // 动态应用部分 - 仅客户端
    '/dashboard/**': { ssr: false },
    '/app/**': { ssr: false },

    // API 路由 - 服务器端
    '/api/**': { cors: true }
  }
})
```

## 手动 SSR 使用 Vue（没有 Nuxt）

```javascript
// server.js - Express 与 Vue SSR
import express from 'express'
import { createSSRApp } from 'vue'
import { renderToString } from 'vue/server-renderer'
import App from './App.vue'

const app = express()

app.get('*', async (req, res) => {
  const vueApp = createSSRApp(App)
  const html = await renderToString(vueApp)

  res.send(`
    <!DOCTYPE html>
    <html>
      <head><title>我的应用</title></head>
      <body>
        <div id="app">${html}</div>
        <script type="module" src="/client.js"></script>
      </body>
    </html>
  `)
})
```

## 性能影响

| 方法 | 首字节时间 | LCP | 需要 JavaScript |
|----------|-------------------|-----|---------------------|
| 客户端 SPA | 快 | 慢（等待 JS）| 是，在内容之前 |
| SSR | 较慢 | 快（HTML 就绪）| 否，用于初始视图 |
| SSG | 快（CDN）| 快（HTML 就绪）| 否，用于初始视图 |

## 何时客户端 SPA 是可接受的

- 内部工具和仪表板（用户期望加载）
- 认证的应用程序（初始加载发生一次）
- 实时协作应用（WebSocket 重）
- 离线优先是优先级的 PWAs
- 复杂的交互式应用（类似 Figma）

## 使用 Vite 进行静态站点生成

```javascript
// vite.config.js - 使用 vite-ssg 进行 SSG
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  ssgOptions: {
    script: 'async',
    formatting: 'minify'
  }
})
```

## 参考
- [Vue.js 性能 - 页面加载优化](https://vuejs.org/guide/best-practices/performance.html#page-load-optimizations)
- [Vue.js SSR 指南](https://vuejs.org/guide/scaling-up/ssr.html)
- [Nuxt.js 文档](https://nuxt.com/)
