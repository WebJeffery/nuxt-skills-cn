---
name: nuxt-seo
description: 专为 Nuxt.js 项目优化 SEO 的技能,提供全面的搜索引擎优化解决方案。
  包括页面元数据优化、结构化数据、站点地图、性能优化、内容策略等。
  适用于 Nuxt 2 和 Nuxt 3 项目,帮助提升网站在搜索引擎中的排名和可见性。

  此技能应在以下情况使用:
  - 用户要求"Nuxt SEO"、"Nuxt 搜索引擎优化"
  - Nuxt 项目需要进行 SEO 优化或部署
  - 需要生成 sitemap、robots.txt 或结构化数据
  - 需要优化页面元数据(title、description、og 标签)
  - 需要改善 Nuxt 应用的 Lighthouse SEO 得分

allowed-tools:
  - Read
  - Write
  - Edit
  - AskUserQuestion
metadata:
  trigger: Nuxt SEO、搜索引擎优化、sitemap、meta 标签、结构化数据
  version: 1.0
---

# Nuxt SEO: Nuxt.js 搜索引擎优化技能

## 技能目的

为 Nuxt.js 项目提供全面的 SEO 优化方案,包括元数据管理、结构化数据、站点地图、性能优化等,帮助提升网站在搜索引擎中的排名。

## 使用时机

### 触发关键词
- "Nuxt SEO"、"Nuxt 搜索引擎优化"
- "Nuxt sitemap"、"Nuxt robots.txt"
- "Nuxt meta 标签"、"Nuxt OG 标签"
- "Nuxt 结构化数据"、"Nuxt Schema.org"
- "优化 Nuxt SEO"、"提升 Nuxt 搜索排名"

### 项目类型
- Nuxt 3 项目 (使用 Nuxt 3 模块)
- Nuxt 2 项目 (使用 @nuxtjs/content 或传统方案)
- 静态生成的 Nuxt 网站 (nuxt generate)
- 服务端渲染的 Nuxt 应用 (nuxt build)

### 优化场景
- 新项目需要配置 SEO 基础设施
- 现有项目 SEO 得分低,需要优化
- 需要生成动态 sitemap 和 robots.txt
- 需要添加结构化数据(JSON-LD)
- 需要优化社交媒体分享卡片(Open Graph)
- 需要实施国际化 SEO(hreflang 标签)

## 核心原则

处理 Nuxt SEO 时遵循以下 5 条原则:

1. **自动化优先** - 使用 Nuxt SEO 模块自动处理常见 SEO 任务
2. **性能为重** - SEO 优化不能牺牲性能(Core Web Vitals)
3. **结构化数据** - 使用 Schema.org 提供丰富的搜索结果
4. **内容可爬取** - 确保搜索引擎能正确抓取和索引内容
5. **渐进增强** - 基础 SEO → 高级 SEO → 国际化 SEO

## Nuxt 3 SEO 配置流程

### Step 1: 安装核心模块

使用 @nuxtjs/seo 模块套件,它集成了多个 SEO 功能:

```bash
npx nuxi@latest module add seo
```

该套件包括:
- @nuxtjs/sitemap: 自动生成 sitemap.xml
- @nuxtjs/robots: 生成 robots.txt
- @nuxtjs/google-fonts: 优化 Google Fonts 加载
- nuxt-schema-org: 结构化数据

### Step 2: 配置基础元数据

在 `nuxt.config.ts` 中配置站点级元数据:

```typescript
export default defineNuxtConfig({
  site: {
    url: 'https://example.com',
    name: '网站名称',
    description: '网站描述',
    defaultLocale: 'zh-CN',
    trailingSlash: false,
  },

  seo: {
    // 启用自动 SEO 优化
    automatic: true,
  },
})
```

### Step 3: 使用 useSeoMeta() 组合函数

在页面或组件中动态设置 SEO 元数据:

```typescript
<script setup lang="ts">
useSeoMeta({
  title: '页面标题',
  description: '页面描述',
  ogTitle: 'Open Graph 标题',
  ogDescription: 'Open Graph 描述',
  ogImage: 'https://example.com/image.jpg',
  twitterCard: 'summary_large_image',
})
</script>
```

### Step 4: 添加结构化数据

使用 useSchemaOrg() 添加结构化数据:

```typescript
<script setup lang="ts">
useSchemaOrg([
  defineWebPage({
    name: '页面名称',
    description: '页面描述',
  }),
  defineArticle({
    // 文章结构化数据
  }),
])
</script>
```

### Step 5: 配置 sitemap 和 robots

自动生成 sitemap.xml 和 robots.txt:

```typescript
export default defineNuxtConfig({
  sitemap: {
    // 自定义 sitemap 配置
    urls: [
      {
        loc: '/about',
        lastmod: new Date(),
        priority: 0.8,
      },
    ],
  },

  robots: {
    allow: '/',
    disallow: ['/admin', '/private'],
  },
})
```

## 常见 SEO 优化任务

### 1. 页面元数据优化

**动态设置页面标题和描述:**

```typescript
// 在页面组件中
useHead({
  title: '页面标题 - 站点名称',
  meta: [
    { name: 'description', content: '页面描述' },
    { property: 'og:title', content: 'Open Graph 标题' },
    { property: 'og:description', content: 'Open Graph 描述' },
    { property: 'og:image', content: '/og-image.jpg' },
  ],
})
```

**使用 useSeoMeta() 的类型安全方式:**

```typescript
useSeoMeta({
  titleTemplate: '%s - 站点名称',
  title: '页面标题',
  description: '页面描述',
  ogTitle: 'OG 标题',
  ogDescription: 'OG 描述',
  ogImage: '/og-image.jpg',
  ogImageAlt: 'OG 图片描述',
  ogType: 'website',
  twitterCard: 'summary_large_image',
})
```

### 2. 结构化数据实现

**文章结构化数据:**

```typescript
useSchemaOrg([
  defineArticle({
    '@type': 'Article',
    headline: '文章标题',
    description: '文章描述',
    image: 'https://example.com/cover.jpg',
    datePublished: '2024-01-01',
    dateModified: '2024-01-02',
    author: [
      {
        '@type': 'Person',
        name: '作者名称',
        url: 'https://example.com/author',
      },
    ],
    publisher: {
      '@type': 'Organization',
      name: '发布者名称',
      logo: {
        '@type': 'ImageObject',
        url: 'https://example.com/logo.jpg',
      },
    },
  }),
])
```

**产品结构化数据:**

```typescript
useSchemaOrg([
  defineProduct({
    name: '产品名称',
    description: '产品描述',
    image: 'https://example.com/product.jpg',
    offers: {
      '@type': 'Offer',
      price: '99.99',
      priceCurrency: 'CNY',
      availability: 'https://schema.org/InStock',
    },
    aggregateRating: {
      '@type': 'AggregateRating',
      ratingValue: '4.8',
      reviewCount: '128',
    },
  }),
])
```

### 3. sitemap.xml 生成

**基础 sitemap 配置:**

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  sitemap: {
    hostname: 'https://example.com',
    gzip: true,
    routes: async () => {
      // 从 API 或数据库动态生成路由
      const posts = await fetch('https://api.example.com/posts').then(r => r.json())
      return posts.map(post => ({
        url: `/blog/${post.slug}`,
        changefreq: 'weekly',
        priority: 0.8,
        lastmod: new Date(post.updatedAt),
      }))
    },
  },
})
```

**多站点 sitemap:**

```typescript
sitemap: {
  sitemaps: {
    blog: {
      include: ['/blog/**'],
      urls: async () => {
        // 动态博客文章
      },
    },
    products: {
      include: ['/products/**'],
    },
  },
}
```

### 4. robots.txt 配置

```typescript
// nuxt.config.ts
robots: {
  UserAgent: '*',
  Allow: '/',
  Disallow: ['/admin', '/api', '/private'],
  Sitemap: 'https://example.com/sitemap.xml',
}
```

**动态 robots.txt:**

```typescript
robots: {
  block: (route) => {
    // 阻止特定路由被索引
    return route.startsWith('/admin') || route.includes('?')
  },
}
```

### 5. 性能优化(Core Web Vitals)

**图片优化:**

```vue
<template>
  <!-- 使用 Nuxt Image 模块优化图片 -->
  <NuxtImg
    src="/image.jpg"
    width="800"
    height="600"
    loading="lazy"
    format="webp"
    alt="图片描述"
  />
</template>
```

**字体优化:**

```typescript
// nuxt.config.ts
googleFonts: {
  families: {
    Roboto: [400, 700],
    'Open+Sans': [300, 400, 600],
  },
  display: 'swap', // 使用字体交换策略
  prefetch: true,
  preconnect: true,
},
```

**预加载关键资源:**

```typescript
useHead({
  link: [
    {
      rel: 'preload',
      as: 'script',
      href: '/critical-script.js',
    },
    {
      rel: 'preconnect',
      href: 'https://api.example.com',
    },
  ],
})
```

### 6. 国际化 SEO(i18n)

**配置多语言 SEO:**

```typescript
// nuxt.config.ts
i18n: {
  locales: [
    {
      code: 'en',
      iso: 'en-US',
      name: 'English',
      domain: 'example.com',
    },
    {
      code: 'zh',
      iso: 'zh-CN',
      name: '���体中文',
      domain: 'zh.example.com',
    },
  ],
  defaultLocale: 'en',
  strategy: 'prefix_except_default',
},
```

**添加 hreflang 标签:**

```typescript
useHead({
  link: [
    {
      rel: 'alternate',
      hreflang: 'en',
      href: 'https://example.com/page',
    },
    {
      rel: 'alternate',
      hreflang: 'zh-CN',
      href: 'https://zh.example.com/page',
    },
    {
      rel: 'alternate',
      hreflang: 'x-default',
      href: 'https://example.com/page',
    },
  ],
})
```

## 工作流程

### Step 1: 分析项目

检查用户的 Nuxt 项目:
- 版本(Nuxt 2 还是 Nuxt 3)
- 现有 SEO 配置
- 项目类型(SSR、静态生成、SPA)
- 需要优化的具体需求

### Step 2: 诊断问题

运行 Lighthouse SEO 审计,识别问题:
- 缺少 meta 标签
- 没有 sitemap 或 robots.txt
- 缺少结构化数据
- 图片缺少 alt 属性
- 性能问题影响 SEO

### Step 3: 实施优化

根据诊断结果实施优化:
1. 安装和配置 @nuxtjs/seo 模块
2. 配置基础元数据
3. 添加结构化数据
4. 生成 sitemap 和 robots.txt
5. 优化页面性能
6. 实施国际化 SEO(如需要)

### Step 4: 验证效果

使用以下工具验证 SEO 优化:
- Google Search Console
- Google Rich Results Test
- Lighthouse SEO 得分
- Schema Markup Validator

## 输出格式

**代码示例输出:**

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  // 配置代码
})
```

**页面组件输出:**

```vue
<template>
  <!-- 模板代码 -->
</template>

<script setup lang="ts">
// SEO 代码
</script>
```

**检查清单:**

- [ ] 安装 @nuxtjs/seo 模块
- [ ] 配置站点元数据
- [ ] 添加页面级 SEO
- [ ] 实施结构化数据
- [ ] 生成 sitemap.xml
- [ ] 生成 robots.txt
- [ ] 优化 Core Web Vitals
- [ ] 配置国际化 SEO(如需要)

## 参考资源

在需要更详细指导时,加载以下参考文件:

- `references/nuxt3-seo-guide.md` - Nuxt 3 SEO 完整指南
- `references/schema-org-types.md` - Schema.org 结构化数据类型
- `references/metadata-best-practices.md` - 元数据最佳实践
- `references/performance-seo.md` - 性能优化与 SEO
- `references/i18n-seo.md` - 国际化 SEO 配置
- `references/common-issues.md` - 常见 SEO 问题解决方案

## 注意事项

1. **版本兼容性** - Nuxt 2 和 Nuxt 3 的 SEO 方案差异较大,确保使用正确的模块
2. **SSR 优先** - SEO 需要 SSR,避免使用 SPA 模式部署
3. **动态内容** - 确保搜索引擎能抓取动态生成的内容
4. **避免重复** - 使用 canonical 标签避免重复内容问题
5. **定期更新** - sitemap 和结构化数据需要随内容更新而更新
6. **测试验证** - 使用 Google 工具验证 SEO 实施效果
