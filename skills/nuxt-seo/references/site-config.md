# 站点配置

为所有 SEO 模块提供共享配置的基础模块。

## 配置

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  site: {
    url: 'https://example.com',       // 绝对 URL 所需
    name: '我的站点',                  // 站点名称(用于标题、schema)
    description: '站点描述',  // 默认 meta 描述
    defaultLocale: 'en',              // 默认语言
    indexable: true,                  // 允许搜索引擎索引
    trailingSlash: false,             // URL 尾部斜杠偏好
  }
})
```

## 基于环境的索引

使用 `NUXT_SITE_*` 环境变量控制每环境索引:

```bash
# .env.production
NUXT_SITE_URL=https://example.com
NUXT_SITE_ENV=production

# .env.staging
NUXT_SITE_URL=https://staging.example.com
NUXT_SITE_ENV=staging
```

模块自动检测 `env` 并为非生产环境设置 `indexable: false`。

对于显式控制:

```ts
export default defineNuxtConfig({
  site: {
    url: process.env.NUXT_SITE_URL,
    // 显式: 仅在显式设置为 'true' 时索引
    indexable: process.env.NUXT_SITE_INDEXABLE === 'true'
  }
})
```

**注意:** `!== 'false'` 在环境变量未定义时默认为 `true` - 使用 `=== 'true'` 以获得故障安全行为。

## 运行时访问

```ts
const site = useSiteConfig()
console.log(site.url, site.name, site.description)
```

在组件、组合式函数和服务器路由中工作。

## i18n 集成

自动与 `@nuxtjs/i18n` 集成:

```ts
export default defineNuxtConfig({
  site: {
    url: 'https://example.com',
    defaultLocale: 'en',
  },
  i18n: {
    locales: [
      { code: 'en', language: 'en-US' },
      { code: 'fr', language: 'fr-FR' },
    ]
  }
})
```

`site` 对象中的语言特定覆盖:

```ts
site: {
  name: '我的站点',
  locales: {
    fr: { name: 'Mon Site' }
  }
}
```

## 每页覆盖

使用路由规则进行页面特定配置:

```ts
export default defineNuxtConfig({
  routeRules: {
    '/admin/**': { site: { indexable: false } },
    '/fr/**': { site: { name: 'Mon Site', defaultLocale: 'fr' } }
  }
})
```
