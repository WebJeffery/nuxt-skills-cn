# 可爬取性: Robots 和 Sitemap

## Robots.txt

在 `/robots.txt` 自动生成。尊重 `site.indexable` 设置。

### 配置

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  robots: {
    // 阻止 AI 爬虫
    blockAiBots: true,
    // 阻止非 SEO 爬虫(减少服务器负载)
    blockNonSeoBots: true,
    // 自定义规则
    groups: [
      { userAgent: '*', disallow: ['/admin'] }
    ]
  }
})
```

### 每页控制

```ts
// 禁用索引
useRobotsRule('noindex, nofollow')

// 带有 AI 指令的对象语法
useRobotsRule({
  noindex: true,
  nofollow: true,
  noai: true,           // 阻止 AI 训练
  noimageai: true,      // 阻止 AI 图像训练
  'max-snippet': 150,   // 预览控制
  'max-image-preview': 'large'
})
```

路由规则:

```ts
export default defineNuxtConfig({
  routeRules: {
    '/admin/**': { robots: 'noindex, nofollow' },
    '/hidden': { robots: false }
  }
})
```

### Nuxt Content Frontmatter

```yaml
---
robots: noindex, nofollow
# 或结构化:
robots:
  noindex: true
  nofollow: true
---
```

## Sitemap.xml

从应用路由在 `/sitemap.xml` 自动生成。

### 配置

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  sitemap: {
    sources: ['/api/__sitemap__/urls'],
    exclude: ['/admin/**', '/secret'],
    // 对于静态站点 - 无运行时生成
    zeroRuntime: true
  }
})
```

### 通过 API 动态 URL

```ts
// server/api/__sitemap__/urls.ts
import { defineSitemapEventHandler } from '#imports'
import type { SitemapUrlInput } from '#sitemap/types'

export default defineSitemapEventHandler(async () => {
  const posts = await $fetch('/api/posts')
  return posts.map(post => ({
    loc: post.path,
    lastmod: post.updatedAt,
    // 图像 sitemap
    images: [{ loc: post.image, title: post.title }],
    // 视频 sitemap
    videos: [{ content_loc: post.videoUrl, title: post.title }]
  } satisfies SitemapUrlInput))
})
```

### 每页控制

路由规则:

```ts
export default defineNuxtConfig({
  routeRules: {
    '/blog/**': { sitemap: { changefreq: 'daily', priority: 0.9 } },
    '/hidden': { sitemap: false }
  }
})
```

Nuxt Content frontmatter:

```yaml
---
sitemap:
  changefreq: weekly
  priority: 0.8
  lastmod: 2025-01-15
---
```

### 多个 Sitemap

对于大型站点:

```ts
export default defineNuxtConfig({
  sitemap: {
    sitemaps: {
      pages: { include: ['/**'], exclude: ['/blog/**'] },
      blog: { include: ['/blog/**'] }
    }
  }
})
```

生成 `/pages-sitemap.xml`、`/blog-sitemap.xml` 和 `/sitemap_index.xml`。

### i18n Sitemap

使用 `@nuxtjs/i18n`,自动生成带有 `hreflang` 替代链接的每语言 sitemap。

## 调试

在开发环境中:

- Robots: 直接检查 `/robots.txt`
- Sitemap: 访问 `/__sitemap__/debug.json` 获取原始数据
