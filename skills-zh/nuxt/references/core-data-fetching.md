---
name: data-fetching
description: 使用 useFetch、useAsyncData 和 $fetch 进行 SSR 友好的数据获取
---

# 数据获取

Nuxt 提供用于 SSR 友好数据获取的 composables，防止重复获取和处理水合。

## 概览

- `$fetch` - 基础获取工具（用于客户端事件）
- `useFetch` - 围绕 $fetch 的 SSR 安全包装器（用于组件数据）
- `useAsyncData` - 围绕任何异步函数的 SSR 安全包装器

## useFetch

用于在组件中获取数据的主要 composable：

```vue
<script setup lang="ts">
const { data, status, error, refresh, clear } = await useFetch('/api/posts')
</script>

<template>
  <div v-if="status === 'pending'">加载中...</div>
  <div v-else-if="error">错误：{{ error.message }}</div>
  <div v-else>
    <article v-for="post in data" :key="post.id">
      {{ post.title }}
    </article>
  </div>
</template>
```

### 带选项

```ts
const { data } = await useFetch('/api/posts', {
  // 查询参数
  query: { page: 1, limit: 10 },
  // 请求体（用于 POST/PUT）
  body: { title: '新文章' },
  // HTTP 方法
  method: 'POST',
  // 只选择特定字段
  pick: ['id', 'title'],
  // 转换响应
  transform: (posts) => posts.map(p => ({ ...p, slug: slugify(p.title) })),
  // 缓存的自定义键
  key: 'posts-list',
  // 不在服务器上获取
  server: false,
  // 不阻塞导航
  lazy: true,
  // 不立即获取
  immediate: false,
  // 默认值
  default: () => [],
})
```

### 刷新和清除

```vue
<script setup lang="ts">
const { data, refresh, clear } = await useFetch('/api/posts')

// 手动刷新
await refresh()

// 清除数据
clear()
</script>

<template>
  <button @click="refresh()">刷新</button>
  <button @click="clear()">清除</button>
</template>
```

## useAsyncData

包装任何异步函数，提供 SSR 水合：

```ts
// 包装任何异步函数
const { data, status, error } = await useAsyncData(
  () => $fetch('/api/posts'),
  { key: 'posts' }
)

// 包装复杂的异步逻辑
const { data } = await useAsyncData(
  async () => {
    const res1 = await $fetch('/api/posts')
    const res2 = await $fetch('/api/users')
    return { posts: res1, users: res2 }
  },
  { transform: (result) => ({ ...result, combined: true })}
)
```

### 用例

- 复杂数据转换
- 组合多个 API 调用
- 缓存计算结果
- 在组件外部使用自定义函数

## $fetch

用于客户端事件的通用 fetch 工具：

```vue
<script setup lang="ts">
const fetchData = async () => {
  // 使用 $fetch 不会水合到服务器
  const data = await $fetch('/api/posts', {
    key: 'posts-client',
    dedupe: 'cancel' // 取消重复请求
  })
  
  // 在客户端事件中使用
  onMounted(fetchData)
}
</script>
<template>
  <button @click="fetchData">获取数据</button>
</template>
```

## 缓存策略

### 使用 useFetch 选项

```ts
// 启用缓存（默认）
const { data } = await useFetch('/api/posts', {
  key: 'posts',
  // 自定义缓存时间
  getCachedData: (key) => useNuxtData(key),
  setCachedData: (key, data) => useNuxtData(key, data, 60000) // 10 分钟
})
```

### 刷新特定数据

```ts
const { refreshNuxtData } = useNuxtData()

// 刷新所有缓存
refreshNuxtData()

// 刷新特定键
refreshNuxtData('posts-list')
```

## 并发请求

```ts
// 并行请求多个资源
const [posts, users] = await Promise.all([
  $fetch('/api/posts'),
  $fetch('/api/users')
])

// 使用 useFetch 确保水合正确
const [{ data: posts }, { data: users }] = await Promise.all([
  useFetch('/api/posts'),
  useFetch('/api/users')
])
```

## 错误处理

```vue
<script setup lang="ts">
const { data, error, status } = await useFetch('/api/posts')

if (error.value) {
  // 服务器错误（4xx, 5xx）
  console.error('服务器错误：', error.value)
} else if (status.value === 'loading') {
  // 加载中状态
}
</script>

<template>
  <div v-if="error">发生错误：{{ error.message }}</div>
  <div v-else-if="data">{{ data }}</div>
</template>
```

## 最佳实践

1. **使用 useFetch 进行组件数据** - 保证 SSR 水合
2. **使用 useAsyncData 包装复杂逻辑** - 提供缓存支持
3. **使用 $fetch 进行客户端事件** - 避免不必要的服务器数据
4. **使用唯一缓存键** - 防止缓存冲突
5. **处理错误和加载状态** - 改善用户体验

## 高级用法

### 监听响应式查询

```ts
const page = ref(1)
const limit = ref(10)

const { data } = await useFetch('/api/posts', {
  query: { page, limit },
  // 当查询变化时重新获取
  watch: [page, limit],
})
```

### 请求取消

```ts
import { useState } from '#imports'

const { data, error } = await useFetch('/api/posts', {
  // 取消之前的请求
  dedupe: 'cancel',
  // 使用 AbortController
  onRequest: ({ options }) => {
    options.signal = new AbortController().signal
  },
})
```

### 超时处理

```ts
const { data, error } = await useFetch('/api/posts', {
  timeout: 5000, // 5 秒超时
  onRequestError: ({ error }) => {
    console.error('请求错误：', error)
  },
})
```

### 重试逻辑

```ts
const { data, error, refresh } = await useFetch('/api/posts', {
  // 自动重试
  retry: 3,
  retryDelay: 1000,
})
```

### 深度数据选择

```ts
const { data } = await useFetch('/api/posts', {
  pick: ['id', 'title', 'author.name'],
  // 使用 pick 过滤数据
  transform: (posts) => posts.map(p => ({
    id: p.id,
    title: p.title,
    author: p.author.name,
  })),
})
```

## 性能优化

### 避免重复请求

```ts
// 使用 key 避免重复请求
const { data } = await useFetch('/api/posts', {
  key: 'posts',
  // 在相同页面中共享数据
  getCachedData: (key) => useNuxtData(key).data,
})
```

### 懒加载

```ts
// 使用 lazy 选项不阻塞导航
const { data } = await useFetch('/api/posts', {
  lazy: true,
})
```

### 预取数据

```vue
<template>
  <div>
    <NuxtLink 
      to="/posts/1" 
      @mouseenter="prefetchPost(1)"
    >
      文章 1
    </NuxtLink>
  </div>
</template>

<script setup>
const prefetchPost = async (id) => {
  await useFetch(`/api/posts/${id}`)
}
</script>
```

## 缓存控制

### 自定义缓存时间

```ts
const { data } = await useFetch('/api/posts', {
  key: 'posts',
  // 缓存 10 分钟
  getCachedData: (key) => {
    const cached = useNuxtData(key).data
    if (cached && Date.now() - cached.timestamp < 600000) {
      return cached.data
    }
    return null
  },
})
```

### 强制刷新

```ts
// 忽略缓存
const { data } = await useFetch('/api/posts', {
  key: 'posts',
  _initial: false, // 强制获取
})
```

## 服务端 vs 客户端获取

### 仅客户端获取

```ts
// 仅在客户端获取数据
const { data } = await useFetch('/api/posts', {
  server: false,
})
```

### 条件获取

```ts
const isAuth = ref(false)

const { data } = await useFetch('/api/user', {
  server: false, // 仅在客户端
  immediate: isAuth.value,
})
```

## 批量请求

### 并行请求

```ts
const [posts, users] = await Promise.all([
  $fetch('/api/posts'),
  $fetch('/api/users'),
])
```

### 顺序请求（依赖）

```ts
const posts = await $fetch('/api/posts')
const comments = await $fetch('/api/comments', {
  query: { postIds: posts.map(p => p.id) },
})
```

## 数据转换

### 转换响应

```ts
const { data } = await useFetch('/api/posts', {
  transform: (posts) => {
    return posts.map(post => ({
      ...post,
      formattedDate: new Date(post.createdAt).toLocaleDateString(),
    }))
  },
})
```

### 使用 pick 和 transform 组合

```ts
const { data } = await useFetch('/api/posts', {
  pick: ['id', 'title', 'createdAt'],
  transform: (posts) => posts.map(p => ({
    ...p,
    url: `/posts/${p.id}`,
  })),
})
```

## 错误处理高级模式

### 全局错误处理

```ts
// plugins/fetch-error.ts
export default defineNuxtPlugin(() => {
  const { data, error } = useFetch('/api/data', {
    onResponseError: ({ response }) => {
      if (response.status === 401) {
        navigateTo('/login')
      }
    },
  })
})
```

### 错误降级

```vue
<script setup>
const { data, error } = await useFetch('/api/posts', {
  default: () => [],
  onRequestError: () => {
    // 返回默认数据
    return { data: [] }
  },
})
</script>
```

## 类型安全

### 定义响应类型

```ts
interface Post {
  id: number
  title: string
  content: string
}

const { data } = await useFetch<Post[]>('/api/posts')
```

### 带类型的转换

```ts
const { data } = await useFetch('/api/posts', {
  transform: (posts: any[]) => {
    return posts.map((p: Post) => ({
      ...p,
      slug: p.title.toLowerCase().replace(/ /g, '-'),
    }))
  },
})
```

## 常见问题

### 水合错误

```ts
// 确保数据在服务端和客户端一致
const { data } = await useFetch('/api/posts', {
  // 使用一致的 key
  key: 'posts',
  // 避免使用客户端特定的数据
  transform: (data) => {
    return data.filter(item => !item.clientOnly)
  },
})
```

### 重复请求

```ts
// 使用 dedupe 避免重复
const { data } = await useFetch('/api/posts', {
  dedupe: 'defer', // 等待当前请求完成
  // 或
  dedupe: 'cancel', // 取消当前请求
})
```

## 性能提示

- 使用 `lazy: true` 避免阻塞导航
- 使用 `immediate: false` 延迟获取
- 合理设置缓存时间
- 使用 `pick` 和 `transform` 减少数据传输
- 考虑使用 `useAsyncData` 进行复杂的数据处理

<!-- 
源码参考：
- https://nuxt.com/docs/getting-started/data-fetching
- https://nuxt.com/docs/api/composables/use-fetch
- https://nuxt.com/docs/api/composables/use-async-data
- https://nuxt.com/docs/api/utils/dollarfetch
-->