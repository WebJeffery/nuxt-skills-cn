---
name: routing
description: 基于文件的路由、动态路由、导航和 Nuxt 中的中间件
---

# 路由

Nuxt 使用基于 vue-router 的文件系统路由。`app/pages/` 目录中的文件会自动创建路由。

## 基础路由

```
pages/
├── index.vue      → /
├── about.vue      → /about
└── posts/
    ├── index.vue  → /posts
    └── [id].vue   → /posts/:id
```

## 动态路由

使用方括号表示动态段：

```
pages/
├── users/
│   └── [id].vue       → /users/:id
├── posts/
│   └── [...slug].vue  → /posts/* (捕获全部)
└── [[optional]].vue   → /:optional? (可选参数)
```

访问路由参数：

```vue
<script setup lang="ts">
const route = useRoute()
// /posts/123 → route.params.id = '123'
console.log(route.params.id)
</script>
```

## 导航

### NuxtLink 组件

```vue
<template>
  <nav>
    <NuxtLink to="/">首页</NuxtLink>
    <NuxtLink to="/about">关于</NuxtLink>
    <NuxtLink :to="{ name: 'posts-id', params: { id: 1 } }">文章 1</NuxtLink>
  </nav>
</template>
```

NuxtLink 会在它们进入视口时自动预取链接的页面。

### 程序化导航

```vue
<script setup lang="ts">
const router = useRouter()

const navigateToAbout = () => {
  router.push('/about')
}

const navigateToPost = (id: number) => {
  router.push({ name: 'posts-id', params: { id } })
}
</script>

<template>
  <button @click="navigateToAbout">去关于页</button>
  <button @click="() => navigateToPost(1)">去文章 1</button>
</template>
```

## 中间件

### 创建中间件

```ts
// middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  // 身份检查逻辑
  if (!isAuthenticated()) {
    return navigateTo('/login')
  }
})
```

### 使用中间件

```vue
<script setup lang="ts">
// 在特定页面中使用中间件
definePageMeta({
  middleware: ['auth']
})
</script>
```

## 路由选项

### 嵌套路由

```
pages/
└── (marketing)/
    └── pricing.vue      → /pricing
```

`marketing/` 是路由组（不会出现在 URL 中）。

### 静态路由

```vue
<script setup lang="ts">
// 访问路由信息
const route = useRoute()
const { path, name, params } = route
</script>
```

### 路由规则

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    '/admin/**': {
      redirect: '/login',
      headers: { 'x-custom-header': 'value' }
    },
    '/api/**': {
      cors: true,
      prerender: false
    },
  },
})
```

## 最佳实践

### 路由组织

- 保持简单和可预测的文件结构
- 使用有意义的文件名
- 对相关路由进行分组

### SEO 优化

```vue
<script setup lang="ts">
useHead({
  title: '我的博客文章',
  meta: {
    description: '阅读我的最新博客文章',
    ogTitle: '我的博客',
    ogImage: 'https://example.com/og-image.jpg'
  }
})
</script>
```

### 懒加载

```vue
<script setup lang="ts">
const route = useRoute()

// 根据路由参数懒加载组件
const { data } = await useFetch(`/api/posts/${route.params.id}`)
</script>

<template>
  <component :is="'LazyPostContent'" v-if="data" :post="data" />
</template>
```

### 错误处理

```vue
<script setup lang="ts">
const error = ref(null)

// 在导航中捕获错误
const navigateWithFallback = async (path: string) => {
  try {
    await navigateTo(path)
  } catch (err) {
    error.value = err
    // 降级逻辑
    await navigateTo('/error')
  }
}
</script>

<template>
  <div v-if="error">发生错误：{{ error.message }}</div>
</template>
```

## 类型安全路由

```ts
// 定义路由参数类型
interface UserParams {
  id: string
  tab?: string
}

// 使用类型安全的路由
const route = useRoute<UserParams>()
const userId = route.params.id
const activeTab = route.query.tab
```

## 导航守卫

```vue
<script setup lang="ts">
const router = useRouter()

const navigateIfAuth = () => {
  if (isAuthenticated()) {
    router.push('/dashboard')
  } else {
    router.push('/login')
  }
}
</script>

<template>
  <button @click="navigateIfAuth">继续</button>
</template>
```

## 高级路由功能

### 路由参数验证

```vue
<script setup lang="ts">
const route = useRoute()

// 验证参数
const postId = computed(() => {
  const id = route.params.id
  if (!id || typeof id !== 'string') {
    throw createError({
      statusCode: 400,
      message: 'Invalid post ID',
    })
  }
  return id
})
</script>
```

### 路由过渡动画

```vue
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'page',
    mode: 'out-in',
  },
  layoutTransition: {
    name: 'layout',
    mode: 'out-in',
  },
})
</script>

<style>
.page-enter-active,
.page-leave-active {
  transition: opacity 0.3s ease;
}

.page-enter-from,
.page-leave-to {
  opacity: 0;
}
</style>
```

### 动态页面标题

```vue
<script setup lang="ts">
const { data } = await useFetch('/api/post')

useHead({
  title: computed(() => data.value?.title || 'Loading...'),
  meta: [
    {
      name: 'description',
      content: computed(() => data.value?.description || ''),
    },
  ],
})
</script>
```

### 路由组

```
pages/
├── (auth)/
│   ├── login.vue      → /login
│   ├── register.vue   → /register
│   └── forgot.vue     → /forgot
├── (app)/
│   ├── index.vue      → /
│   └── settings.vue   → /settings
```

### 嵌套路由

```
pages/
├── parent/
│   ├── index.vue      → /parent
│   └── child.vue      → /parent/child
```

```vue
<!-- parent/index.vue -->
<template>
  <div>
    <h1>Parent</h1>
    <NuxtPage />
  </div>
</template>
```

### 路由别名

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  alias: {
    '/old-path': '/new-path',
  },
})
```

### 路由重定向

```ts
export default defineNuxtRouteMiddleware((to) => {
  if (to.path === '/old-path') {
    return navigateTo('/new-path', { redirectCode: 301 })
  }
})
```

## 中间件高级用法

### 全局中间件

```ts
// middleware/analytics.global.ts
export default defineNuxtRouteMiddleware((to, from) => {
  // 在每个路由上运行
  console.log('Navigating to:', to.path)
})
```

### 中间件链

```vue
<script setup>
definePageMeta({
  middleware: [
    'auth',
    'admin',
    'logger',
  ],
})
</script>
```

### 中间件传参

```ts
// middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
  const requireRole = to.meta.requireRole as string | undefined
  
  if (requireRole && !hasRole(requireRole)) {
    return navigateTo('/login')
  }
})
```

```vue
<script setup>
definePageMeta({
  middleware: 'auth',
  requireRole: 'admin',
})
</script>
```

### 异步中间件

```ts
export default defineNuxtRouteMiddleware(async (to, from) => {
  const user = await $fetch('/api/user')
  
  if (!user) {
    return navigateTo('/login')
  }
})
```

## 导航选项

### 带状态的导航

```vue
<script setup lang="ts">
const router = useRouter()

const navigateWithState = () => {
  router.push({
    path: '/search',
    query: {
      q: 'nuxt',
      page: 1,
      sort: 'date',
    },
  })
}
</script>
```

### 编程式导航选项

```ts
const router = useRouter()

router.push('/about')
router.replace('/home')
router.back()
router.go(-1)

// 带选项
router.push('/about', {
  onDone: () => console.log('Navigation complete'),
  onCancel: () => console.log('Navigation cancelled'),
})
```

### 导航错误处理

```ts
const router = useRouter()

const safeNavigate = async (path: string) => {
  try {
    await router.push(path)
  } catch (error) {
    console.error('Navigation failed:', error)
  }
}
```

## 路由元信息

```vue
<script setup lang="ts">
definePageMeta({
  layout: 'admin',
  middleware: ['auth'],
  keepAlive: true,
  title: 'Admin Dashboard',
  key: route => route.fullPath,
  pageTransition: {
    name: 'fade',
  },
})
</script>
```

## 路由类型安全

### 定义路由类型

```ts
// types/router.d.ts
declare module '#app' {
  interface RouteNamedMap {
    'index': {}
    'posts': {}
    'posts-id': { id: string }
    'users-id-posts-postId': {
      id: string
      postId: string
    }
  }
}
```

### 类型安全的导航

```vue
<script setup lang="ts">
const router = useRouter()

// 类型安全的导航
router.push({ name: 'posts-id', params: { id: '123' } })

// 访问类型安全的参数
const route = useRoute('posts-id')
const id = route.params.id // string
</script>
```

## 性能优化

### 预加载路由

```vue
<template>
  <NuxtLink 
    to="/about" 
    prefetch 
    prefetch-on="visibility"
  >
    关于
  </NuxtLink>
</template>
```

### 懒加载组件

```vue
<script setup lang="ts">
const LazyComponent = defineAsyncComponent(() =>
  import('~/components/HeavyComponent.vue')
)
</script>

<template>
  <LazyComponent v-if="showComponent" />
</template>
```

## 常见问题

### 路由参数丢失

**原因：** 刷新页面时参数丢失

**解决：**

```ts
const route = useRoute()
const params = computed(() => route.params)
```

### 水合不匹配

**原因：** 服务器和客户端路由状态不一致

**解决：**

```ts
// 使用 key 强制重新渲染
<NuxtPage :key="route.fullPath" />
```

### 中间件不执行

**原因：** 中间件文件命名错误

**解决：**

```
middleware/
├── auth.ts              ✅ 正确
├── auth.global.ts       ✅ 全局
└── auth.middleware.ts  ❌ 错误
```

<!-- 
源码参考：
- https://nuxt.com/docs/getting-started/routing
- https://nuxt.com/docs/api/composables/use-route
- https://nuxt.com/docs/api/utils/navigations
-->