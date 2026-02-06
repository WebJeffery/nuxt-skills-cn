# 元数据最佳实践

## Title 标签

**长度:** 50-60 个字符

**格式:** `页面标题 | 站点名称`

```typescript
useSeoMeta({
  title: 'Nuxt 3 SEO 完全指南',
  titleTemplate: '%s - My Site',
})
```

## Meta Description

**长度:** 150-160 个字符

```typescript
useSeoMeta({
  description: '学习如何在 Nuxt 3 中实施 SEO 优化。涵盖元数据、结构化数据、sitemap、robots.txt 等。',
})
```

## Open Graph 标签

```typescript
useSeoMeta({
  ogTitle: 'Open Graph 标题',
  ogDescription: 'Open Graph 描述',
  ogImage: 'https://example.com/image.jpg',
  ogType: 'website',
})
```

## Twitter Card

```typescript
useSeoMeta({
  twitterCard: 'summary_large_image',
  twitterTitle: 'Twitter 标题',
  twitterDescription: 'Twitter 描述',
  twitterImage: 'https://example.com/image.jpg',
})
```

## Canonical 标签

```typescript
useHead({
  link: [
    {
      rel: 'canonical',
      href: 'https://example.com/page',
    },
  ],
})
```

## Hreflang 标签

```typescript
useHead({
  link: [
    {
      rel: 'alternate',
      hreflang: 'en',
      href: 'https://example.com/en/page',
    },
    {
      rel: 'alternate',
      hreflang: 'zh-CN',
      href: 'https://example.com/zh/page',
    },
    {
      rel: 'alternate',
      hreflang: 'x-default',
      href: 'https://example.com/page',
    },
  ],
})
```

## 验证工具

- [Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/)
- [Twitter Card Validator](https://cards-dev.twitter.com/validator)
- [LinkedIn Post Inspector](https://www.linkedin.com/post-inspector/)
