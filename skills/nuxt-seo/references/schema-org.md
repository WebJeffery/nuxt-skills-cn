# Schema.org 结构化数据

用于丰富搜索结果的 JSON-LD 结构化数据。

## 站点身份

在 `nuxt.config.ts` 中配置一次:

```ts
import { defineOrganization } from 'nuxt-schema-org/schema'

export default defineNuxtConfig({
  schemaOrg: {
    identity: defineOrganization({
      name: '我的公司',
      url: 'https://example.com',
      logo: '/logo.png',
      sameAs: ['https://twitter.com/mycompany', 'https://github.com/mycompany']
    })
  }
})
```

对于个人站点:

```ts
import { definePerson } from 'nuxt-schema-org/schema'

export default defineNuxtConfig({
  schemaOrg: {
    identity: definePerson({
      name: 'John Doe',
      url: 'https://johndoe.com',
      image: '/avatar.jpg',
      sameAs: ['https://twitter.com/johndoe']
    })
  }
})
```

## 页面级 Schema

定义函数在组件中**自动导入**(无需导入):

```ts
// 文章页面
useSchemaOrg([
  defineArticle({
    headline: '我的文章标题',
    description: '文章描述',
    image: '/article-image.jpg',
    datePublished: '2025-01-15',
    dateModified: '2025-01-20',
    author: { name: 'John Doe', url: 'https://johndoe.com' }
  })
])
```

```ts
// 产品页面(在 offers 中包含 url 以进行 Google 验证)
useSchemaOrg([
  defineProduct({
    name: '产品名称',
    description: '产品描述',
    image: '/product.jpg',
    offers: {
      price: 99.99,
      priceCurrency: 'USD',
      availability: 'InStock',
      url: 'https://example.com/product'
    }
  })
])
```

## 定义函数

| 函数                | 用例               |
| ----------------------- | ---------------------- |
| `defineArticle()`       | 博客文章,新闻       |
| `defineProduct()`       | 电商产品    |
| `defineFAQPage()`       | FAQ 页面              |
| `defineHowTo()`         | 教程/指南页面   |
| `defineRecipe()`        | 食谱页面           |
| `defineEvent()`         | 事件                 |
| `defineLocalBusiness()` | 商业信息          |
| `defineVideo()`         | 视频内容          |
| `defineBreadcrumb()`    | 面包屑导航  |
| `defineWebPage()`       | 通用页面           |
| `defineWebSite()`       | 站点范围(自动添加)       |
| `defineJobPosting()`    | 职位列表           |
| `defineSoftwareApp()`   | 软件/应用          |
| `defineService()`       | 服务               |

## 数据推断

模块从页面 head 自动推断:

- `title` → WebPage 名称
- `description` → WebPage 描述
- `og:image` → WebPage 图像

## 面包屑

从路由路径自动生成,或自定义:

```ts
useSchemaOrg([
  defineBreadcrumb({
    itemListElement: [
      { name: '首页', item: '/' },
      { name: '博客', item: '/blog' },
      { name: '我的文章', item: '/blog/my-post' }
    ]
  })
])
```

或使用 `useBreadcrumbItems()` 组合式函数(来自 seo-utils):

```ts
const items = useBreadcrumbItems()
useSchemaOrg([defineBreadcrumb({ itemListElement: items })])
```

## FAQ 页面

```ts
useSchemaOrg([
  defineFAQPage({
    mainEntity: [
      { name: '您的退货政策是什么?', acceptedAnswer: '您可以在 30 天内退货。' },
      { name: '如何联系支持?', acceptedAnswer: '给我们发送邮件 support@example.com' }
    ]
  })
])
```

## Nuxt Content

Frontmatter:

```yaml
---
title: 我的文章
schemaOrg:
  - type: BlogPosting
    headline: 我的文章
    datePublished: 2025-01-15
    author:
      type: Person
      name: John Doe
---
```

使用 `asSeoCollection()` (参见主 SKILL.md),确保 schema 渲染:

```vue
<script setup>
const { data: page } = await useAsyncData(() => queryCollection('posts').path(route.path).first())
useHead(page.value?.head || {})
</script>
```

## 调试和验证

- 调试端点: 开发环境中的 `/__schema-org__/debug.json`
- 配置: `schemaOrg: { debug: true }`
- [Google 富媒体结果测试](https://search.google.com/test/rich-results)
- [Schema.org 验证器](https://validator.schema.org/)

## 路由规则

```ts
export default defineNuxtConfig({
  routeRules: {
    '/blog/**': {
      schemaOrg: { type: 'Article' }
    }
  }
})
```
