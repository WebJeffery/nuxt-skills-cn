# Nuxt 国际化 SEO 配置

## 快速开始

```bash
npm install @nuxtjs/i18n
```

```typescript
export default defineNuxtConfig({
  modules: ['@nuxtjs/i18n'],

  i18n: {
    locales: [
      { code: 'en', iso: 'en-US', name: 'English' },
      { code: 'zh', iso: 'zh-CN', name: '简体中文' },
    ],
    defaultLocale: 'en',
    strategy: 'prefix_except_default',
  },
})
```

## URL 策略

### prefix_except_default

```typescript
strategy: 'prefix_except_default'
```

- 英语: `example.com/about`
- 中文: `example.com/zh/about`

### prefix

```typescript
strategy: 'prefix'
```

- 英语: `example.com/en/about`
- 中文: `example.com/zh/about`

### no_prefix (不同域名)

```typescript
strategy: 'no_prefix'
differentDomains: true

locales: [
  { code: 'en', domain: 'example.com' },
  { code: 'zh', domain: 'zh.example.com' },
]
```

## Hreflang 标签

自动生成:

```typescript
export default defineNuxtConfig({
  i18n: {
    seo: true,
  },
})
```

## 本地化元数据

```typescript
const { t } = useI18n()

useSeoMeta({
  title: t('page.title'),
  description: t('page.description'),
})
```

## 语言文件

```typescript
// locales/en-US.ts
export default {
  home: {
    title: 'Home',
    description: 'Welcome',
  },
}

// locales/zh-CN.ts
export default {
  home: {
    title: '首页',
    description: '欢迎',
  },
}
```

## 本地化 Sitemap

```typescript
export default defineNuxtConfig({
  sitemap: {
    hostname: 'https://example.com',
    i18n: true,
  },
})
```
