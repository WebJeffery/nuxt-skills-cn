# Nuxt SEO 常见问题解决方案

## 页面不被索引

### noindex 标签

```typescript
useSeoMeta({
  robots: 'index, follow',
})
```

### robots.txt 阻止

```typescript
export default defineNuxtConfig({
  robots: {
    UserAgent: '*',
    Allow: '/',
    Disallow: ['/admin'],
  },
})
```

## 重复内容问题

### URL 参数差异

```typescript
const route = useRoute()

useHead({
  link: [
    {
      rel: 'canonical',
      href: `https://example.com${route.path}`,
    },
  ],
})
```

### 分页

```typescript
useHead({
  link: [
    page.value > 1 && {
      rel: 'prev',
      href: `https://example.com/page/${page.value - 1}`,
    },
    page.value < totalPages.value && {
      rel: 'next',
      href: `https://example.com/page/${page.value + 1}`,
    },
  ].filter(Boolean),
})
```

## sitemap 不生成

### 模块未安装

```bash
npm install @nuxtjs/sitemap
```

```typescript
export default defineNuxtConfig({
  modules: ['@nuxtjs/sitemap'],
  sitemap: {
    hostname: 'https://example.com',
  },
})
```

### 动态路由

```typescript
export default defineNuxtConfig({
  sitemap: {
    routes: async () => {
      const posts = await $fetch('/api/posts')
      return posts.map((post: any) => ({
        url: `/blog/${post.slug}`,
        lastmod: post.updatedAt,
      }))
    },
  },
})
```

## 结构化数据错误

### 使用正确格式

```typescript
useSchemaOrg([
  defineArticle({
    headline: '标题',
    image: 'https://example.com/image.jpg',
    datePublished: '2024-01-01',
    author: {
      '@type': 'Person',
      name: '作者',
    },
  }),
])
```

## 调试工具

- Google Search Console
- Rich Results Test
- Schema Markup Validator
- Lighthouse SEO Audit

```bash
# 测试 sitemap
curl https://example.com/sitemap.xml

# 测试 robots.txt
curl https://example.com/robots.txt
```
