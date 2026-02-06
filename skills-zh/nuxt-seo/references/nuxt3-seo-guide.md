# Nuxt 3 SEO 完整指南

## 目录

1. [快速开始](#快速开始)
2. [核心模块安装](#核心模块安装)
3. [配置详解](#配置详解)
4. [最佳实践](#最佳实践)
5. [高级用法](#高级用法)

## 快速开始

### 安装 @nuxtjs/seo

```bash
npx nuxi@latest module add seo
```

这个命令会安装以下模块:
- `@nuxtjs/sitemap` v5
- `@nuxtjs/robots` v3
- `nuxt-schema-org` v3
- `@nuxtjs/google-fonts` v3

### 基础配置

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@nuxtjs/seo'],

  site: {
    url: 'https://example.com',
    name: 'My Site',
    description: 'My awesome site',
    defaultLocale: 'en',
  },
})
```

## 核心模块安装

### 1. @nuxtjs/sitemap

自动生成 sitemap.xml 文件。

**安装:**
```bash
npm install @nuxtjs/sitemap
```

**配置:**
```typescript
export default defineNuxtConfig({
  sitemap: {
    hostname: 'https://example.com',
    gzip: true,
    routes: async () => {
      const products = await fetch('https://api.example.com/products').then(r => r.json())
      return products.map(p => ({
        url: `/products/${p.id}`,
        changefreq: 'daily',
        priority: 0.8,
        lastmod: new Date(p.updatedAt),
      }))
    },
  },
})
```

### 2. @nuxtjs/robots

生成 robots.txt 文件。

**配置:**
```typescript
export default defineNuxtConfig({
  robots: {
    UserAgent: '*',
    Allow: '/',
    Disallow: ['/admin', '/private'],
    Sitemap: 'https://example.com/sitemap.xml',
  },
})
```

### 3. nuxt-schema-org

添加结构化数据(JSON-LD)。

**使用:**
```vue
<script setup lang="ts">
useSchemaOrg([
  defineWebPage({
    name: 'Page Name',
  }),
  defineArticle({
    headline: 'Article Title',
    image: 'https://example.com/image.jpg',
    datePublished: '2024-01-01',
    author: {
      '@type': 'Person',
      name: 'Author Name',
    },
  }),
])
</script>
```

### 4. @nuxtjs/google-fonts

优化 Google Fonts 加载。

**配置:**
```typescript
export default defineNuxtConfig({
  googleFonts: {
    families: {
      Roboto: [400, 700],
      'Open+Sans': [300, 400, 600],
    },
    display: 'swap',
    prefetch: true,
    preconnect: true,
  },
})
```

## 配置详解

### 站点元数据

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  site: {
    url: 'https://example.com',
    name: 'Site Name',
    description: 'Site Description',
    defaultLocale: 'en',
    trailingSlash: false, // URL 是否以 / 结尾
    identity: {
      type: 'Organization', // 或 'Person'
      logo: 'https://example.com/logo.png',
    },
    twitter: '@username', // Twitter 用户名
  },
})
```

### 全局 SEO 配置

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  seo: {
    // 启用自动 SEO 优化
    automatic: true,

    // 自定义 meta 标签
    meta: {
      charset: 'utf-8',
      viewport: 'width=device-width, initial-scale=1',
    },
  },
})
```

## 最佳实践

### 1. 使用 useSeoMeta()

**类型安全的 SEO 元数据设置:**

```typescript
<script setup lang="ts">
useSeoMeta({
  // 基础元数据
  title: 'Page Title',
  description: 'Page description for SEO',

  // Open Graph
  ogTitle: 'OG Title',
  ogDescription: 'OG Description',
  ogImage: 'https://example.com/image.jpg',
  ogImageAlt: 'Image description',
  ogType: 'website',

  // Twitter Card
  twitterCard: 'summary_large_image',
  twitterTitle: 'Twitter Title',
  twitterDescription: 'Twitter Description',
  twitterImage: 'https://example.com/twitter-image.jpg',

  // 其他
  robots: 'index, follow',
  googlebot: 'index, follow',
})
</script>
```

### 2. 使用 useHead()

**更灵活的 head 标签管理:**

```typescript
<script setup lang="ts">
useHead({
  title: 'Page Title',

  meta: [
    { name: 'description', content: 'Page description' },
    { property: 'og:title', content: 'OG Title' },
    { property: 'og:description', content: 'OG Description' },
    { property: 'og:image', content: '/og-image.jpg' },
    { name: 'twitter:card', content: 'summary_large_image' },
  ],

  link: [
    { rel: 'canonical', href: 'https://example.com/page' },
    { rel: 'alternate', hreflang: 'en', href: 'https://example.com/en/page' },
    { rel: 'alternate', hreflang: 'zh', href: 'https://example.com/zh/page' },
  ],

  script: [
    {
      type: 'application/ld+json',
      children: JSON.stringify({
        '@context': 'https://schema.org',
        '@type': 'WebPage',
        name: 'Page Name',
      }),
    },
  ],
})
</script>
```

### 3. 动态元数据

**基于数据动态设置 SEO:**

```vue
<script setup lang="ts">
const { data: post } = await useFetch('/api/posts/1')

if (post.value) {
  useSeoMeta({
    title: post.value.title,
    description: post.value.excerpt,
    ogImage: post.value.coverImage,
  })

  useSchemaOrg([
    defineArticle({
      headline: post.value.title,
      description: post.value.excerpt,
      image: post.value.coverImage,
      datePublished: post.value.createdAt,
      author: {
        '@type': 'Person',
        name: post.value.author.name,
      },
    }),
  ])
}
</script>
```

## 高级用法

### 1. 多 sitemap

```typescript
export default defineNuxtConfig({
  sitemap: {
    sitemaps: {
      blog: {
        include: ['/blog/**'],
        routes: async () => {
          const posts = await fetch('/api/posts').then(r => r.json())
          return posts.map(p => ({
            loc: `/blog/${p.slug}`,
            lastmod: p.updatedAt,
          }))
        },
      },
      products: {
        include: ['/products/**'],
      },
    },
  },
})
```

### 2. 动态 robots.txt

```typescript
export default defineNuxtConfig({
  robots: {
    allow: '/',
    disallow: ['/admin', '/api'],

    // 动态规则
    block: (route) => {
      // 阻止包含查询参数的 URL
      return route.includes('?')
    },

    // 多用户代理
    userAgent: [
      {
        UserAgent: 'Googlebot',
        Allow: '/',
      },
      {
        UserAgent: '*',
        Disallow: '/admin',
      },
    ],
  },
})
```

### 3. 结构化数据组合

```typescript
useSchemaOrg([
  defineWebPage({
    name: 'About Us',
    description: 'Learn about our company',
  }),
  defineOrganization({
    name: 'Company Name',
    logo: 'https://example.com/logo.png',
    url: 'https://example.com',
    sameAs: [
      'https://twitter.com/company',
      'https://facebook.com/company',
    ],
  }),
  defineBreadcrumb({
    itemListElement: [
      { name: 'Home', item: 'https://example.com' },
      { name: 'About', item: 'https://example.com/about' },
    ],
  }),
])
```

### 4. 国际化 SEO

```typescript
export default defineNuxtConfig({
  i18n: {
    locales: [
      { code: 'en', iso: 'en-US', domain: 'example.com' },
      { code: 'zh', iso: 'zh-CN', domain: 'zh.example.com' },
    ],
    defaultLocale: 'en',
  },

  // 自动生成 hreflang 标签
  seo: {
    automatic: true,
  },
})
```

### 5. 性能优化

**图片优化:**
```vue
<template>
  <NuxtImg
    src="/image.jpg"
    width="800"
    height="600"
    loading="lazy"
    format="webp"
    alt="Description"
  />
</template>
```

**预加载关键资源:**
```typescript
useHead({
  link: [
    {
      rel: 'preload',
      as: 'font',
      type: 'font/woff2',
      crossorigin: 'anonymous',
      href: '/fonts/custom-font.woff2',
    },
    {
      rel: 'preconnect',
      href: 'https://api.example.com',
    },
  ],
})
```

## 常见问题

### Q: 如何避免重复内容?

使用 canonical 标签:

```typescript
useHead({
  link: [
    {
      rel: 'canonical',
      href: 'https://example.com/original-page',
    },
  ],
})
```

### Q: 如何处理分页 SEO?

```typescript
useHead({
  link: [
    {
      rel: 'prev',
      href: 'https://example.com/page/1',
    },
    {
      rel: 'next',
      href: 'https://example.com/page/3',
    },
  ],
})
```

### Q: 如何添加验证代码?

```typescript
useHead({
  meta: [
    {
      name: 'google-site-verification',
      content: 'YOUR_VERIFICATION_CODE',
    },
    {
      name: 'baidu-site-verification',
      content: 'YOUR_BAIDU_CODE',
    },
  ],
})
```

## 调试工具

- Google Search Console
- Google Rich Results Test
- Schema Markup Validator
- Lighthouse SEO Audit
- Chrome DevTools Lighthouse

## 参考链接

- [Nuxt SEO Documentation](https://nuxtseo.com/)
- [Schema.org](https://schema.org/)
- [Google Search Central](https://developers.google.com/search)
