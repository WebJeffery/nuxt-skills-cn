# SEO 工具

来自 nuxt-seo-utils 和 nuxt-link-checker 的其他工具。

## 规范 URL

基于站点配置的自动规范 URL。

```ts
export default defineNuxtConfig({
  seoUtils: {
    canonicalQueryWhitelist: ['page', 'sort'],  // 保留这些查询参数
    redirectToCanonicalSiteUrl: true  // 301 重定向到规范域名
  }
})
```

每页覆盖:

```ts
useHead({
  link: [{ rel: 'canonical', href: 'https://example.com/preferred-url' }]
})
```

## 面包屑

从当前路由生成面包屑项目:

```ts
const items = useBreadcrumbItems()
// [{ label: '首页', to: '/' }, { label: '博客', to: '/blog' }, { label: '我的文章' }]
```

对于 schema.org 集成,参见 [schema-org.md](schema-org.md#breadcrumbs)。

在模板中渲染:

```vue
<template>
  <nav aria-label="面包屑">
    <ol class="flex gap-2">
      <li v-for="(item, i) in items" :key="i">
        <NuxtLink v-if="item.to" :to="item.to">{{ item.label }}</NuxtLink>
        <span v-else>{{ item.label }}</span>
      </li>
    </ol>
  </nav>
</template>
```

在路由 meta 中自定义标签:

```ts
// pages/blog/[slug].vue
definePageMeta({
  breadcrumb: { label: '文章' }
})
```

## 标题模板

设置站点范围标题模板:

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  app: {
    head: {
      titleTemplate: '%s | 我的站点'
    }
  }
})
```

每页覆盖:

```ts
useHead({
  title: '页面标题',
  titleTemplate: '%s - 不同模板'
})
```

## Meta 默认值

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  app: {
    head: {
      meta: [
        { name: 'author', content: '我的名字' },
        { property: 'og:site_name', content: '我的站点' }
      ]
    }
  }
})
```

## 链接检查器

构建时链接验证。

```ts
export default defineNuxtConfig({
  linkChecker: {
    failOnError: true,  // 默认: 错误时构建失败
    exclude: ['/api/**'],
    skipInspections: ['missing-hash'],
    report: { html: true }  // 生成 HTML 报告
  }
})
```

**检查项:**

- `no-error-response` - 404/500 错误
- `no-baseless` - 缺少基础 URL
- `no-javascript` - javascript: 链接
- `trailing-slash` - 不一致的斜杠
- `missing-hash` - 无效的锚点目标
- `no-uppercase-chars` - URL 大小写
- `absolute-site-urls` - 硬编码域名

### 忽略链接

```html
<a href="/maybe-broken" data-link-checker-ignore>链接</a>
```

## 基于文件的图标

将 favicon 文件放在 `public/` 中:

```
public/
├── favicon.ico
├── favicon.svg        # 现代浏览器
├── apple-touch-icon.png
└── site.webmanifest
```

自动检测并添加到 `<head>`。

对于支持暗模式的 SVG favicon:

```svg
<!-- public/favicon.svg -->
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32">
  <style>
    path { fill: #000; }
    @media (prefers-color-scheme: dark) {
      path { fill: #fff; }
    }
  </style>
  <path d="..."/>
</svg>
```

## 社交 Meta 标签

从站点配置自动生成 Open Graph 和 Twitter 卡片(参见 [site-config.md](site-config.md))。

每页覆盖:

```ts
useSeoMeta({
  title: '页面标题',
  description: '页面描述',
  ogImage: '/images/page-og.png',
  twitterCard: 'summary_large_image'
})
```

## 尾部斜杠重定向

强制一致的 URL:

```ts
export default defineNuxtConfig({
  site: {
    trailingSlash: false  // 重定向 /blog/ 到 /blog
  }
})
```

## 调试面板

启用全面的调试面板:

```ts
export default defineNuxtConfig({
  seo: { debug: true }
})
```

在开发中显示:

- 当前 meta 标签
- Schema.org 数据
- OG 图像预览
- Sitemap/robots 状态
