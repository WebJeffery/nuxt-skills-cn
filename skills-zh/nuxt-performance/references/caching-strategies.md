# 缓存策略完整指南

## 缓存的重要性

**性能提升:**
- 首次访问: 3.5s
- 缓存命中: 0.5s
- **提升: 7倍**

## HTTP 缓存

### 1. Cache-Control 头

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    // 静态资源: 长期缓存
    '/_nuxt/**': {
      headers: {
        'Cache-Control': 'public, max-age=31536000, immutable'
      }
    },

    // 图片: 1 年
    '/images/**': {
      headers: {
        'Cache-Control': 'public, max-age=31536000, immutable'
      }
    },

    // API 响应: 短期缓存
    '/api/articles': {
      cache: {
        maxAge: 60 * 60, // 1 小时
      }
    },

    // 永不缓存
    '/api/user/profile': {
      headers: {
        'Cache-Control': 'no-store, no-cache, must-revalidate'
      }
    },
  },
})
```

### 2. 服务端缓存

```typescript
// server/api/articles.ts
export default defineEventHandler(async (event) => {
  // 使用 Nitro 存储
  const cached = await storage.getItem('articles')

  if (cached) {
    return cached
  }

  // 获取数据
  const articles = await fetchArticles()

  // 缓存 1 小时
  await storage.setItem('articles', articles, { ttl: 3600 })

  return articles
})
```

### 3. 边缘缓存

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    routeRules: {
      // Vercel 边缘缓存
      '/': { isr: true },

      // Cloudflare KV
      '/api/**': {
        cache: {
          maxAge: 60 * 60,
          handles: ['GET', 'HEAD'],
        },
      },
    },
  },
})
```

## 数据缓存

### 1. useAsyncData 缓存

```typescript
// pages/index.vue
<script setup lang="ts">
// 基础缓存
const { data, refresh } = await useAsyncData('articles', () =>
  $fetch('/api/articles'),
  {
    // 5 分钟内不会重新请求
    transform: (data) => data.articles,
  }
)

// 手动缓存控制
const { data } = await useAsyncData('user', () =>
  $fetch('/api/user'),
  {
    // 使用缓存的数据
    getCachedData: (key) => useNuxtData(key).data,

    // 1 分钟后过期
    default: () => null,
  }
)
</script>
```

### 2. useFetch 缓存

```typescript
// composables/useArticles.ts
export function useArticles() {
  const { data, pending, error, refresh } = await useFetch('/api/articles', {
    // 缓存键
    key: 'articles',

    // 5 分钟缓存
    getCachedData: (key) => useNuxtData(key).data,

    // 手动刷新
    dedupe: 'cancel',
  })

  return {
    articles: data,
    pending,
    error,
    refresh,
  }
}
```

### 3. 自定义缓存

```typescript
// composables/useCache.ts
const cache = new Map<string, { data: any, timestamp: number }>()

export function useCache<T>(
  key: string,
  fetcher: () => Promise<T>,
  ttl: number = 60000
) {
  const cached = cache.get(key)

  if (cached && Date.now() - cached.timestamp < ttl) {
    return cached.data
  }

  const data = fetcher()

  cache.set(key, {
    data,
    timestamp: Date.now(),
  })

  return data
}

// 使用
const articles = await useCache('articles', fetchArticles, 60000)
```

## 组件缓存

### 1. Keep-Alive

```vue
<template>
  <!-- 缓存组件状态 -->
  <KeepAlive :include="['HomePage', 'AboutPage']">
    <component :is="currentPage" />
  </KeepAlive>
</template>

<script setup lang="ts">
const currentPage = ref('HomePage')
</script>
```

### 2. 组件级缓存

```vue
<script setup lang="ts">
// 使用 defineOptions
defineOptions({
  // 不缓存
  inheritAttrs: false,
})
</script>
```

### 3. 计算属性缓存

```typescript
// composables/useExpensive.ts
export function useExpensiveCalculation(data: Ref<Data[]>) {
  // 计算属性自动缓存
  const result = computed(() => {
    return expensiveOperation(data.value)
  })

  return { result }
}
```

## 路由缓存

### 1. 页面缓存

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    // SSG + ISR
    '/': { isr: 3600 },

    // 完全静态
    '/about': { isr: true },

    // 混合渲染
    '/blog/**': { isr: 7200 },
  },
})
```

### 2. 预渲染缓存

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    prerender: {
      routes: ['/sitemap.xml', '/robots.txt'],
      ignore: ['/admin/**'],
      concurrency: 10,
    },
  },
})
```

### 3. 路由参数缓存

```typescript
// pages/posts/[id].vue
<script setup lang="ts">
const route = useRoute()

// 根据 ID 缓存
const { data } = await useAsyncData(`post-${route.params.id}`, () =>
  $fetch(`/api/posts/${route.params.id}`)
)
</script>
```

## 高级缓存策略

### 1. SWR (Stale-While-Revalidate)

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    '/api/articles': {
      headers: {
        // 后台重新验证
        'Cache-Control': 's-maxage=60, stale-while-revalidate=300'
      }
    },
  },
})
```

### 2. 缓存层级

```typescript
// server/api/data.ts
export default defineEventHandler(async (event) => {
  // 1. 检查浏览器缓存
  // (通过 Cache-Control)

  // 2. 检查边缘缓存
  const edgeCached = await getEdgeCache('data')
  if (edgeCached) return edgeCached

  // 3. 检查服务器缓存
  const serverCached = await storage.getItem('data')
  if (serverCached) return serverCached

  // 4. 获取新数据
  const data = await fetchData()

  // 更新缓存
  await setEdgeCache('data', data, { ttl: 300 })
  await storage.setItem('data', data, { ttl: 600 })

  return data
})
```

### 3. 智能缓存失效

```typescript
// server/api/articles/[id].ts
export default defineEventHandler(async (event) => {
  const id = getRouterParam(event, 'id')

  // 更新文章
  if (event.method === 'PUT') {
    const updated = await updateArticle(id, await readBody(event))

    // 失效相关缓存
    await storage.removeItem('articles')
    await storage.removeItem(`article-${id}`)

    return updated
  }
})
```

## CDN 缓存

### 1. Vercel 缓存

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    // 静态页面
    '/': { isr: true },

    // 增量静态再生成
    '/blog/**': { isr: 3600 },

    // API 路由
    '/api/**': {
      cache: {
        maxAge: 60,
        staleWhileRevalidate: 300,
      },
    },
  },
})
```

### 2. Cloudflare 缓存

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    vercel: {
      config: {
        cache: {
          maxAge: 3600,
        },
      },
    },
  },
})
```

### 3. 自定义 CDN

```typescript
// server/api/image.ts
export default defineEventHandler(async (event) => {
  const url = getQuery(event).url as string

  // 从 CDN 获取
  const cached = await fetchFromCDN(url)

  if (cached) {
    // 设置 CDN 缓存头
    setHeader(event, 'Cache-Control', 'public, max-age=31536000')
    return cached
  }

  // 原始图片
  const image = await fetch(url).then(r => r.blob())

  // 上传到 CDN
  await uploadToCDN(url, image)

  return image
})
```

## 缓存监控

### 1. 缓存命中率

```typescript
// plugins/cache-monitor.ts
export default defineNuxtPlugin(() => {
  if (process.env.NODE_ENV === 'development') {
    let hits = 0
    let misses = 0

    // 监控 useAsyncData
    const originalUseAsyncData = useAsyncData

    // @ts-ignore
    globalThis.useAsyncData = async (...args) => {
      const [key] = args

      const cached = useNuxtData(key).data

      if (cached.value) {
        hits++
        console.log(`[Cache Hit] ${key}`)
      } else {
        misses++
        console.log(`[Cache Miss] ${key}`)
      }

      const result = await originalUseAsyncData(...args)

      console.log(`Cache Hit Rate: ${(hits / (hits + misses) * 100).toFixed(2)}%`)

      return result
    }
  }
})
```

### 2. 缓存大小监控

```typescript
// composables/useCacheWithStats.ts
const cacheStats = ref({
  size: 0,
  keys: 0,
})

export function useCacheWithStats<T>(key: string, fetcher: () => Promise<T>) {
  const result = await useCache(key, fetcher)

  // 更新统计
  cacheStats.value.size = JSON.stringify(result).length
  cacheStats.value.keys++

  return result
}
```

## 最佳实践

### ✅ 推荐做法

1. **分层缓存**
   ```typescript
   // 浏览器 > CDN > 服务器
   ```

2. **设置合理的 TTL**
   ```typescript
   // 静态资源: 1 年
   // API: 1-60 分钟
   // 动态数据: 不缓存
   ```

3. **使用 SWR**
   ```typescript
   // 先返回缓存,后台更新
   cache: 's-maxage=60, stale-while-revalidate=300'
   ```

4. **缓存键设计**
   ```typescript
   // 包含所有相关参数
   key: `articles-${category}-${page}`
   ```

### ❌ 避免做法

1. **过度缓存**
   ```typescript
   // ❌ 个人数据长期缓存
   '/api/profile': { cache: { maxAge: 31536000 } }

   // ✅ 短期或不缓存
   '/api/profile': { cache: { maxAge: 60 } }
   ```

2. **缓存失效问题**
   ```typescript
   // ❌ 更新后未失效缓存
   await updateArticle(id)

   // ✅ 失效相关缓存
   await storage.removeItem(`article-${id}`)
   ```

3. **忽略移动端**
   ```typescript
   // ✅ 移动端使用更短的 TTL
   const ttl = isMobile ? 60 : 300
   ```

## 检查清单

```markdown
## 缓存策略清单

### HTTP 缓存
- [ ] 静态资源长期缓存 (1 年)
- [ ] API 响应短期缓存 (1-60 分钟)
- [ ] 动态数据不缓存
- [ ] 设置 ETag

### 数据缓存
- [ ] useAsyncData 缓存配置
- [ ] 服务端缓存 (Nitro storage)
- [ ] 自定义缓存策略

### 组件缓存
- [ ] Keep-Alive 组件
- [ ] 计算属性缓存
- [ ] 避免不必要的重渲染

### CDN 缓存
- [ ] 配置边缘缓存
- [ ] 设置 CDN 缓存规则
- [ ] 监控缓存命中率

### 缓存监控
- [ ] 缓存命中率统计
- [ ] 缓存大小监控
- [ ] 缓存失效策略
```

## 参考资源

- [HTTP Caching](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)
- [Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control)
- [Nuxt Route Rules](https://nuxt.com/docs/guide/concepts/rendering#route-rules)
- [Vercel Caching](https://vercel.com/docs/concepts/edge-network/caching)
