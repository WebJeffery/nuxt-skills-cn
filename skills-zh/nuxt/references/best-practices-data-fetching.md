---
name: data-fetching-best-practices
description: Nuxt 中高效数据获取的模式和最佳实践
---

# 数据获取最佳实践

SSR 友好、高性能 Nuxt 应用的有效数据获取模式。

## 选择合适的工具

| 场景 | 使用 |
|----------|-----|
| 组件初始数据 | `useFetch` 或 `useAsyncData` |
| 用户交互（点击、表单） | `$fetch` |
| 第三方 SDK/API | `useAsyncData` 配合自定义函数 |
| 多个并行请求 | `useAsyncData` 配合 `Promise.all` |

## Await 与非 Await 使用

`await` 关键字控制数据获取是否**阻塞导航**：

### 使用 `await` - 阻塞导航

```vue
<script setup lang="ts">
// 导航等待直到数据获取完成（使用 Vue Suspense）
const { data } = await useFetch('/api/posts')
// data.value 在此行之后立即可用
</script>
```

- **服务器**：获取数据并将其包含在 payload 中
- **客户端水合**：使用 payload 数据，不重新获取
- **客户端导航**：阻塞直到数据就绪

### 不使用 `await` - 非阻塞（懒加载）

```vue
<script setup lang="ts">
// 导航立即继续，数据在后台获取
const { data, status } = useFetch('/api/posts', { lazy: true })
// data.value 最初可能未定义 - 检查 status！
</script>

<template>
  <div v-if="status === 'pending'">加载中...</div>
  <div v-else>{{ data }}</div>
</template>
```

等同于使用 `useLazyFetch`：

```vue
<script setup lang="ts">
const { data, status } = useLazyFetch('/api/posts')
</script>
```

### 何时使用每种方式

| 模式 | 使用场景 |
|---------|----------|
| `await useFetch()` | SEO/初始渲染需要的关键数据 |
| `useFetch({ lazy: true })` | 非关键数据，更好的感知性能 |
| `await useLazyFetch()` | 与 lazy 相同，await 仅确保初始化 |

## 避免双重获取

### ❌ 错误：在 setup 中单独使用 $fetch

```vue
<script setup lang="ts">
// 这会获取两次：一次在服务器，一次在客户端
const data = await $fetch('/api/posts')
</script>
```

### ✅ 正确：使用 useFetch

```vue
<script setup lang="ts">
// 在服务器获取，在客户端水合（无双重获取）
const { data } = await useFetch('/api/posts')
</script>
```

## 使用显式缓存键

### ❌ 避免：自动生成的键

```vue
<script setup lang="ts">
// 键从文件/行自动生成 - 可能导致问题
const { data } = await useAsyncData(() => fetchPosts())
</script>
```

### ✅ 更好：显式键

```vue
<script setup lang="ts">
// 显式键以实现可预测的缓存
const { data } = await useAsyncData(
  'posts',
  () => fetchPosts(),
)

// 参数化数据的动态键
const route = useRoute()
const { data: post } = await useAsyncData(
  `post-${route.params.id}`,
  () => fetchPost(route.params.id),
)
</script>
```

## 正确处理加载状态

```vue
<script setup lang="ts">
const { data, status, error } = await useFetch('/api/posts')
</script>

<template>
  <div v-if="status === 'pending'">
    <SkeletonLoader />
  </div>
  <div v-else-if="error">
    <ErrorMessage :error="error" />
  </div>
  <div v-else>
    <PostList :posts="data" />
  </div>
</template>
```

## 对非关键数据使用懒加载

```vue
<script setup lang="ts">
const id = useRoute().params.id

// 关键数据 - 阻塞导航
const { data: post } = await useFetch(`/api/posts/${id}`)

// 非关键数据 - 不阻塞导航
const { data: comments, status } = useFetch(`/api/posts/${id}/comments`, {
  lazy: true,
})

// 或使用 useLazyFetch
const { data: related } = useLazyFetch(`/api/posts/${id}/related`)
</script>

<template>
  <article>
    <h1>{{ post?.title }}</h1>
    <p>{{ post?.content }}</p>
  </article>

  <section v-if="status === 'pending'">加载评论中...</section>
  <CommentList v-else :comments="comments" />
</template>
```

## 最小化 Payload 大小

### 使用 `pick` 进行简单过滤

```vue
<script setup lang="ts">
const { data } = await useFetch('/api/users', {
  // 仅在 payload 中包含这些字段
  pick: ['id', 'name', 'avatar'],
})
</script>
```

### 使用 `transform` 进行复杂转换

```vue
<script setup lang="ts">
const { data } = await useFetch('/api/posts', {
  transform: (posts) => {
    return posts.map(post => ({
      id: post.id,
      title: post.title,
      excerpt: post.content.slice(0, 100),
      date: new Date(post.createdAt).toLocaleDateString(),
    }))
  },
})
</script>
```

## 并行获取

### 使用 useAsyncData 获取独立数据

```vue
<script setup lang="ts">
const { data } = await useAsyncData(
  'dashboard',
  async (_nuxtApp, { signal }) => {
    const [user, posts, stats] = await Promise.all([
      $fetch('/api/user', { signal }),
      $fetch('/api/posts', { signal }),
      $fetch('/api/stats', { signal }),
    ])
    return { user, posts, stats }
  },
)
</script>
```

### 多个 useFetch 调用

```vue
<script setup lang="ts">
// 这些自动并行运行
const [{ data: user }, { data: posts }] = await Promise.all([
  useFetch('/api/user'),
  useFetch('/api/posts'),
])
</script>
```

## 高效刷新模式

### 监听响应式依赖

```vue
<script setup lang="ts">
const page = ref(1)
const category = ref('all')

const { data } = await useFetch('/api/posts', {
  query: { page, category },
  // 当这些变化时自动刷新
  watch: [page, category],
})
</script>
```

### 手动刷新

```vue
<script setup lang="ts">
const { data, refresh, status } = await useFetch('/api/posts')

async function refreshPosts() {
  await refresh()
}
</script>
```

### 条件获取

```vue
<script setup lang="ts">
const userId = ref<string | null>(null)

const { data, execute } = useFetch(() => `/api/users/${userId.value}`, {
  immediate: false, // 在 userId 设置之前不获取
})

// 稍后，当 userId 可用时
function loadUser(id: string) {
  userId.value = id
  execute()
}
</script>
```

## 仅服务器获取

```vue
<script setup lang="ts">
// 仅在服务器获取，跳过客户端导航
const { data } = await useFetch('/api/static-content', {
  server: true,
  lazy: true,
  getCachedData: (key, nuxtApp) => nuxtApp.payload.data[key],
})
</script>
```

## 错误处理

```vue
<script setup lang="ts">
const { data, error, refresh } = await useFetch('/api/posts')

// 如果需要类似事件的处理，则监听错误
watch(error, (err) => {
  if (err) {
    console.error('获取失败:', err)
    // 显示 toast、重定向等
  }
}, { immediate: true })
</script>

<template>
  <div v-if="error">
    <p>加载失败: {{ error.message }}</p>
    <button @click="refresh()">重试</button>
  </div>
</template>
```

## 跨组件共享数据

```vue
<!-- ComponentA.vue -->
<script setup lang="ts">
const { data } = await useFetch('/api/user', { key: 'current-user' })
</script>

<!-- ComponentB.vue -->
<script setup lang="ts">
// 访问缓存数据而不重新获取
const { data: user } = useNuxtData('current-user')

// 或刷新它
const { refresh } = await useFetch('/api/user', { key: 'current-user' })
</script>
```

## 避免在 useAsyncData 中使用副作用

### ❌ 错误：在 useAsyncData 中使用副作用

```vue
<script setup lang="ts">
// 不要触发 Pinia 操作或副作用
await useAsyncData(() => store.fetchUser()) // 可能导致问题
</script>
```

### ✅ 正确：使用 callOnce 处理副作用

```vue
<script setup lang="ts">
await callOnce(async () => {
  await store.fetchUser()
})
</script>
```

## 高级模式

### 客户端缓存控制

```vue
<script setup lang="ts">
const { data, refresh } = await useFetch('/api/posts', {
  // 自定义缓存策略
  getCachedData: (key, nuxtApp) => {
    return nuxtApp.payload.data[key] || nuxtApp.static.data[key]
  },
  // 设置缓存时间
  dedupe: 'cancel', // 'cancel' | 'defer'
})
</script>
```

### 使用 AbortController

```vue
<script setup lang="ts">
const { data, error, refresh } = await useFetch('/api/posts', {
  // 接收 abort signal
  onRequest({ request, options }) {
    options.signal = request.signal
  },
})

// 手动取消
const controller = new AbortController()
refreshNuxtData('posts')
controller.abort()
</script>
```

### 深度监听变化

```vue
<script setup lang="ts">
const search = ref('')
const page = ref(1)

const { data, refresh } = await useFetch('/api/posts', {
  query: {
    q: search,
    page,
  },
  // 监听多个响应式变量
  watch: [search, page],
  // 防抖（毫秒）
  watchDebounce: 300,
})

// 或自定义监听器
watch([search, page], ([newSearch, newPage]) => {
  refresh()
}, { debounce: 300 })
</script>
```

### 响应式查询参数

```vue
<script setup lang="ts">
const route = useRoute()

const { data, error } = await useFetch('/api/posts', {
  // 使用 getter 函数获取响应式查询
  query: () => ({
    id: route.params.id,
    tab: route.query.tab,
  }),
})
</script>
```

### 并发数据获取

```vue
<script setup lang="ts">
// 使用 Promise.all 并行获取独立数据
const { data } = await useAsyncData(
  'dashboard',
  async (_nuxtApp, { signal }) => {
    const [user, posts, stats] = await Promise.all([
      $fetch('/api/user', { signal }),
      $fetch('/api/posts', { signal }),
      $fetch('/api/stats', { signal }),
    ])
    return { user, posts, stats }
  },
  {
    // 单个失败时重试
    onResponseError({ request, options, response }) {
      if (response.status === 401) {
        return navigateTo('/login')
      }
    },
  },
)
</script>
```

### 数据转换和验证

```vue
<script setup lang="ts">
const { data, error } = await useFetch('/api/posts', {
  // 服务器端和客户端端都会运行
  transform(input) {
    return input.map(post => ({
      id: post.id,
      title: post.title,
      // 计算派生属性
      slug: post.title.toLowerCase().replace(/\s+/g, '-'),
    }))
  },
  // 客户端水合时运行（用于验证）
  transform: (input) => {
    if (import.meta.client) {
      // 验证服务器传来的数据
      if (!Array.isArray(input)) {
        throw new Error('Invalid data format')
      }
    }
    return input
  },
})
</script>
```

### 条件数据获取

```vue
<script setup lang="ts">
const enabled = ref(false)

const { data, execute, refresh } = useFetch('/api/data', {
  // 不立即获取
  immediate: false,
  // 基于 enable 值获取
  query: {
    enabled,
  },
})

// 稍后获取
function loadData() {
  enabled.value = true
  execute()
}
</script>
```

### 分页和无限滚动

```vue
<script setup lang="ts">
const page = ref(1)
const perPage = 10

const { data: posts, pending, refresh } = await useFetch(
  'posts',
  () => $fetch('/api/posts', {
    query: {
      page,
      limit: perPage,
    },
  },
)

function loadMore() {
  page.value++
}

function reset() {
  page.value = 1
  refresh()
}

// 自动加载更多（无限滚动）
onMounted(() => {
  const observer = new IntersectionObserver((entries) => {
    if (entries[0].isIntersecting && !pending.value) {
      loadMore()
    }
  })

  observer.observe(document.querySelector('#load-more-trigger'))
})
</script>

<template>
  <div v-for="post in posts" :key="post.id">
    {{ post.title }}
  </div>
  <button
    id="load-more-trigger"
    @click="loadMore"
    :disabled="pending"
  >
    {{ pending ? 'Loading...' : 'Load More' }}
  </button>
</template>
```

### 实时数据更新

```vue
<script setup lang="ts">
const { data, refresh, suspend } = await useFetch('/api/posts')

// 暂停自动刷新
suspend()

// 恢复自动刷新
resume()

// 手动刷新
async function manualRefresh() {
  await refresh()
}

// 监听新数据（WebSocket、事件等）
onMounted(() => {
  const ws = new WebSocket('ws://api.example.com')

  ws.onmessage = () => {
    // 刷新数据
    refresh()
  }
})
</script>
```

### 请求/响应拦截

```vue
<script setup lang="ts">
const { data } = await useFetch('/api/data', {
  // 请求前
  onRequest({ request, options }) {
    // 添加认证头
    options.headers = {
      ...options.headers,
      Authorization: `Bearer ${getToken()}`,
    }
  },

  // 请求错误
  onRequestError({ request, options, error }) {
    console.error('Request error:', error)
    // 重试逻辑
    if (error.name === 'TimeoutError') {
      return retryRequest(options)
    }
  },

  // 响应后
  onResponse({ request, response, options }) {
    // 记录响应时间
    console.log('Response time:', response.headers.get('x-response-time'))
  },

  // 响应错误
  onResponseError({ request, options, response }) {
    if (response.status === 401) {
      navigateTo('/login')
    } else if (response.status === 403) {
      showErrorToast('Access denied')
    }
  },
})
</script>
```

## 性能优化技巧

### 避免重复请求

```vue
<script setup lang="ts">
// ❌ 不好的做法：每次组件挂载都获取
const Component1 = defineComponent({
  async setup() {
    const { data } = await useFetch('/api/user')
  },
})

const Component2 = defineComponent({
  async setup() {
    const { data } = await useFetch('/api/user') // 重复请求！
  },
})

// ✅ 好的做法：使用相同键共享数据
const Component1 = defineComponent({
  async setup() {
    const { data } = await useFetch('/api/user', { key: 'user' })
  },
})

const Component2 = defineComponent({
  async setup() {
    const { data: user } = useNuxtData('user') // 复用缓存
  },
})
</script>
```

### 数据预取

```vue
<script setup lang="ts">
// 预取即将访问的数据
const route = useRoute()

// 当前数据
const { data: post } = await useFetch(`/api/posts/${route.params.id}`)

// 预取可能的下一页
const { data: nextPost } = await useFetch(
  `post-${parseInt(route.params.id) + 1}`,
  () => $fetch(`/api/posts/${parseInt(route.params.id) + 1}`),
  { lazy: true },
)
</script>
```

### 选择性刷新

```vue
<script setup lang="ts">
const { data: user } = await useFetch('/api/user', { key: 'user' })
const { data: posts } = await useFetch('/api/posts', { key: 'posts' })

// 只刷新 posts，不刷新 user
function refreshPosts() {
  refreshNuxtData('posts')
}

// 刷新所有
function refreshAll() {
  refreshNuxtData()
}
</script>
```

## 常见问题

### Q: 何时使用 useFetch vs $fetch？

**A:**
- `useFetch` / `useAsyncData`: SSR 友好，避免双重获取
- `$fetch`: 用户交互（按钮点击、表单提交），不需要 SSR

### Q: 如何处理加载状态？

**A:**
```vue
<script setup>
const { data, pending, error } = useFetch('/api/data')
</script>

<template>
  <div v-if="pending">Loading...</div>
  <div v-else-if="error">Error: {{ error.message }}</div>
  <div v-else>{{ data }}</div>
</template>
```

### Q: 如何取消进行中的请求？

**A:**
```vue
<script setup>
const { refresh } = useFetch('/api/data')
const controller = new AbortController()

async function load() {
  try {
    await refresh({ signal: controller.signal })
  } catch (err) {
    if (err.name === 'AbortError') {
      console.log('Request cancelled')
    }
  }
}

function cancel() {
  controller.abort()
  controller = new AbortController()
}
</script>
```

<!--
Source references:
- https://nuxt.com/docs/getting-started/data-fetching
- https://nuxt.com/docs/api/composables/use-fetch
- https://nuxt.com/docs/api/composables/use-async-data
- https://nuxt.com/docs/api/composables/use-lazy-fetch
-->
