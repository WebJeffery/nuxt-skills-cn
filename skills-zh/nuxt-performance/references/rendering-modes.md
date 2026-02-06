# Nuxt 渲染模式选择指南

## 渲染模式概述

Nuxt 3 提供多种渲染模式,每种模式都有不同的性能特征和使用场景。

## 渲染模式对比

### 1. SSG (Static Site Generation)

**定义:** 预先构建为静态 HTML 文件

**性能:** ⚡️⚡️⚡️⚡️⚡️ (最快)

**适用场景:**
- 营销网站
- 博客/文档
- 作品集
- 不经常变化的内容

**配置:**

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  ssr: true,
  nitro: {
    preset: 'static', // 或 'vercel-static', 'netlify-static'
  },
})
```

```bash
# 生成静态站点
npm run generate

# 预览
npm run preview
```

**优点:**
- ✅ 极快的加载速度
- ✅ CDN 友好
- ✅ 无服务器成本
- ✅ 高可靠性
- ✅ 最佳 SEO

**缺点:**
- ❌ 内容需要重新构建才能更新
- ❌ 不适合动态内容

**性能指标:**
- LCP: < 1.0s
- FCP: < 0.5s
- TTI: < 1.5s

---

### 2. ISR (Incremental Static Regeneration)

**定义:** 静态生成 + 按需更新

**性能:** ⚡️⚡️⚡️⚡️ (很快)

**适用场景:**
- 电商网站
- 新闻网站
- 需要定期更新的内容

**配置:**

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    // ISR: 1 小时更新一次
    '/blog/**': { isr: 3600 },

    // ISR: 1 天更新一次
    '/products/**': { isr: 86400 },

    // 后台重新验证
    '/api/articles': {
      isr: true,
      headers: {
        'Cache-Control': 's-maxage=60, stale-while-revalidate=300'
      }
    },
  },
})
```

**优点:**
- ✅ 接近 SSG 的性能
- ✅ 支持内容更新
- ✅ 不会出现构建失败
- ✅ SEO 友好

**缺点:**
- ❌ 需要服务器支持
- ❌ 更新有延迟
- ❌ 缓存管理复杂

**性能指标:**
- LCP: < 1.5s
- FCP: < 0.8s
- TTI: < 2.0s

---

### 3. SSR (Server-Side Rendering)

**定义:** 每次请求都在服务器渲染

**性能:** ⚡️⚡️⚡️ (中等)

**适用场景:**
- 动态内容网站
- 需要实时数据的应用
- 个性化内容
- 管理后台

**配置:**

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  ssr: true,
})
```

```typescript
// 特定路由使用 SSR
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    '/admin/**': { ssr: true },
    '/api/**': { ssr: true },
  },
})
```

**优点:**
- ✅ 内容实时更新
- ✅ SEO 友好
- ✅ 支持个性化
- ✅ 社交媒体友好

**缺点:**
- ❌ 服务器成本高
- ❌ TTFB 较长
- ❌ 需要服务器资源
- ❌ 扩展性受限

**性能指标:**
- LCP: < 2.0s
- FCP: < 1.2s
- TTI: < 3.0s

---

### 4. CSR (Client-Side Rendering)

**定义:** 完全在浏览器渲染

**性能:** ⚡️⚡️ (较慢)

**适用场景:**
- 管理后台
- 需要复杂交互的应用
- 不需要 SEO 的页面
- 受限的路由

**配置:**

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  ssr: false, // 或 spa: true
})

// 或特定路由
export default defineNuxtConfig({
  routeRules: {
    '/app/**': { ssr: false },
    '/dashboard/**': { spa: true },
  },
})
```

**优点:**
- ✅ 快速导航
- ✅ 服务器成本低
- ✅ 丰富的交互
- ✅ 离线支持

**缺点:**
- ❌ 首屏加载慢
- ❌ SEO 不友好
- ❌ 社交媒体分享差
- ❌ 白屏时间长

**性能指标:**
- LCP: < 3.5s
- FCP: < 2.0s
- TTI: < 5.0s

---

### 5. 混合渲染 (Hybrid)

**定义:** 根据路由选择不同渲染模式

**性能:** ⚡️⚡️⚡️⚡️ (灵活)

**适用场景:**
- 大型网站
- 不同页面需求不同
- 需要平衡性能和功能

**配置:**

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  ssr: true,

  routeRules: {
    // 首页: SSG
    '/': { isr: true },

    // 博客: ISR (1 小时)
    '/blog/**': { isr: 3600 },

    // 产品页面: ISR (1 天)
    '/products/**': { isr: 86400 },

    // 管理后台: CSR
    '/admin/**': { ssr: false },

    // API: 缓存
    '/api/**': { cache: { maxAge: 60 } },

    // 用户资料: SSR (实时)
    '/profile/**': { ssr: true },

    // 仪表板: CSR
    '/dashboard/**': { spa: true },
  },
})
```

**优点:**
- ✅ 最佳性能平衡
- ✅ 灵活性高
- ✅ 成本效益好
- ✅ 满足各种需求

**缺点:**
- ❌ 配置复杂
- ❌ 需要深入理解
- ❌ 调试困难

---

## 选择决策树

```
需要 SEO?
├─ 是 → 内容经常变化?
│       ├─ 否 → SSG (静态站点)
│       └─ 是 → 可以接受更新延迟?
│               ├─ 是 → ISR (增量静态生成)
│               └─ 否 → SSR (服务端渲染)
└─ 否 → 需要实时数据?
        ├─ 否 → CSR (客户端渲染)
        └─ 是 → SSR + CSR 混合
```

## 实际应用案例

### 案例 1: 企业官网

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  ssr: true,
  nitro: {
    preset: 'static',
  },
})
```

**选择理由:**
- 内容不经常变化
- 需要最佳 SEO
- 访问量大,成本低

---

### 案例 2: 电商网站

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    // 首页和分类: ISR
    '/': { isr: 600 },
    '/categories/**': { isr: 3600 },

    // 产品页: ISR (1 天)
    '/products/**': { isr: 86400 },

    // 搜索: SSR
    '/search': { ssr: true },

    // 购物车和结账: CSR
    '/cart': { ssr: false },
    '/checkout': { ssr: false },

    // 用户账户: SSR
    '/account/**': { ssr: true },
  },
})
```

**选择理由:**
- 平衡性能和实时性
- 关键页面快速加载
- 个性化内容使用 SSR/CSR

---

### 案例 3: SaaS 应用

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    // 营销页面: SSG
    '/': { isr: true },
    '/pricing': { isr: true },
    '/docs/**': { isr: 3600 },

    // 应用: CSR
    '/app/**': { ssr: false },
    '/dashboard/**': { ssr: false },

    // API: 缓存
    '/api/public/**': { cache: { maxAge: 300 } },
  },
})
```

**选择理由:**
- 营销页面需要 SEO
- 应用部分需要快速交互
- 降低服务器负载

---

### 案例 4: 新闻网站

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    // 首页: ISR (5 分钟)
    '/': { isr: 300 },

    // 文章列表: ISR (10 分钟)
    '/articles': { isr: 600 },
    '/category/**': { isr: 600 },

    // 单篇文章: ISR (1 小时)
    '/articles/**': { isr: 3600 },

    // 实时新闻: SSR
    '/breaking-news': { ssr: true },
  },
})
```

**选择理由:**
- 内容频繁更新
- 需要 SEO
- 平衡性能和时效性

---

## 性能优化技巧

### 1. 预渲染

```bash
# 预渲染特定路由
npm run generate -- --crawl
```

### 2. 缓存策略

```typescript
// server/api/news.ts
export default defineEventHandler(async (event) => {
  // 使用 Nitro 存储
  const cached = await storage.getItem('news')

  if (cached) {
    return cached
  }

  const news = await fetchNews()
  await storage.setItem('news', news, { ttl: 300 })

  return news
})
```

### 3. CDN 配置

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  app: {
    // CDN URL
    baseURL: 'https://cdn.example.com',
    cdnURL: 'https://cdn.example.com',
  },
})
```

### 4. 预取数据

```typescript
// 预取下一页数据
const prefetchNextPage = () => {
  const link = document.createElement('link')
  link.rel = 'prefetch'
  link.href = '/api/articles?page=2'
  document.head.appendChild(link)
}
```

---

## 迁移指南

### 从 CSR 迁移到 SSR

```typescript
// 1. 启用 SSR
export default defineNuxtConfig({
  ssr: true,
})

// 2. 检查浏览器专用代码
if (process.client) {
  // 浏览器代码
}

// 3. 使用 onMounted
onMounted(() => {
  // 客户端代码
})
```

### 从 SSR 迁移到 SSG

```typescript
// 1. 启用静态生成
export default defineNuxtConfig({
  nitro: {
    preset: 'static',
  },
})

// 2. 移除实时数据依赖
// 使用 data 数据或静态 API

// 3. 生成静态站点
npm run generate
```

---

## 参考资源

- [Nuxt Rendering Modes](https://nuxt.com/docs/guide/concepts/rendering)
- [Route Rules](https://nuxt.com/docs/guide/concepts/rendering#route-rules)
- [Nitro Presets](https://nitro.unjs.io/config/#preset)
