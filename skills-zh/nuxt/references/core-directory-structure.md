---
name: directory-structure
description: Nuxt 项目文件夹结构、约定和文件组织
---

# 目录结构

Nuxt 使用基于约定的目录结构。理解它对于高效开发至关重要。

## 标准项目结构

```
my-nuxt-app/
├── app/                    # 应用源代码（可以在根级别）
│   ├── app.vue             # 根组件
│   ├── app.config.ts       # 应用配置（运行时）
│   ├── error.vue           # 错误页面
│   ├── components/         # 自动导入的 Vue 组件
│   ├── composables/        # 自动导入的 composables
│   ├── layouts/            # 布局组件
│   ├── middleware/         # 路由中间件
│   ├── pages/              # 基于文件的路由
│   ├── plugins/            # Vue 插件
│   └── utils/              # 自动导入的工具
├── assets/                 # 构建处理的资源（CSS、图片）
├── public/                 # 静态资源（按原样服务）
├── server/                 # 服务器端代码
│   ├── api/                # API 路由（/api/*）
│   ├── routes/             # 服务器路由
│   ├── middleware/         # 服务器中间件
│   ├── plugins/            # Nitro 插件
│   └── utils/              # 服务器工具（自动导入）
├── content/                # 内容文件（@nuxt/content）
├── layers/                 # 本地层（自动扫描）
├── modules/                # 本地模块
├── nuxt.config.ts          # Nuxt 配置
├── package.json
└── tsconfig.json
```

## 关键目录

### `app/` 目录

包含所有应用代码。也可以在根级别（没有 `app/` 文件夹）。

```ts
// nuxt.config.ts - 自定义源目录
export default defineNuxtConfig({
  srcDir: 'src/', // 从 'app/' 改为 'src/'
})
```

### `app/components/`

按名称自动导入的 Vue 组件：

```
components/
├── Button.vue           → <Button />
├── Card.vue             → <Card />
├── base/
│   └── Button.vue       → <BaseButton />
├── ui/
│   ├── Input.vue        → <UiInput />
│   └── Modal.vue        → <UiModal />
└── TheHeader.vue        → <TheHeader />
```

**懒加载**：使用 `Lazy` 前缀进行动态导入：

```vue
<template>
  <LazyHeavyChart v-if="showChart" />
</template>
```

**客户端/服务器专用**：

```
components/
├── Comments.client.vue  → 仅在客户端渲染
└── ServerData.server.vue → 仅在服务器渲染
```

### `app/composables/`

自动导入的 Vue composables（仅顶层文件）：

```
composables/
├── useAuth.ts           → useAuth()
├── useFoo.ts            → useFoo()
└── nested/
    └── utils.ts         → 不会自动导入
```

重新导出嵌套 composables：

```ts
// composables/index.ts
export { useHelper } from './nested/utils'
```

### `app/pages/`

基于文件的路由：

```
pages/
├── index.vue            → /
├── about.vue            → /about
├── blog/
│   ├── index.vue        → /blog
│   └── [slug].vue       → /blog/:slug
├── users/
│   └── [id]/
│       └── profile.vue  → /users/:id/profile
├── [...slug].vue        → /*（捕获全部）
├── [[optional]].vue     → /:optional?（可选参数）
└── (marketing)/         → 路由组（不在 URL 中）
    └── pricing.vue      → /pricing
```

**Pages 是可选的**：没有 `pages/` 时，不包含 vue-router。

### `app/layouts/`

包装页面的布局组件：

```
layouts/
├── default.vue          → 默认布局
├── admin.vue            → 管理员布局
└── blank.vue            → 无布局
```

```vue
<!-- layouts/default.vue -->
<template>
  <div>
    <TheHeader />
    <slot />
    <TheFooter />
  </div>
</template>
```

在页面中使用：

```vue
<script setup>
definePageMeta({
  layout: 'admin',
  // layout: false // 禁用布局
})
</script>
```

### `app/middleware/`

路由中间件：

```
middleware/
├── auth.ts              → 命名中间件
├── admin.ts             → 命名中间件
└── logger.global.ts     → 全局中间件（在每个路由上运行）
```

### `app/plugins/`

Nuxt 插件（自动注册）：

```
plugins/
├── 01.analytics.ts      → 使用数字前缀排序
├── 02.auth.ts
├── vue-query.client.ts  → 仅客户端插件
└── server-init.server.ts → 仅服务器插件
```

### `server/` 目录

Nitro 服务器代码：

```
server/
├── api/
│   ├── users.ts         → GET /api/users
│   ├── users.post.ts    → POST /api/users
│   └── users/[id].ts    → /api/users/:id
├── routes/
│   └── sitemap.xml.ts   → /sitemap.xml
├── middleware/
│   └── auth.ts          → 在每个请求上运行
├── plugins/
│   └── db.ts            → 服务器启动插件
└── utils/
    └── db.ts            → 自动导入的服务器工具
```

### `public/` 目录

在根 URL 提供的静态资源：

```
public/
├── favicon.ico          → /favicon.ico
├── robots.txt           → /robots.txt
└── images/
    └── logo.png         → /images/logo.png
```

### `assets/` 目录

构建处理的资源：

```
assets/
├── css/
│   └── main.css
├── images/
│   └── hero.png
└── fonts/
    └── custom.woff2
```

在组件中引用：

```vue
<template>
  <img src="~/assets/images/hero.png" />
</template>

<style>
@import '~/assets/css/main.css';
</style>
```

## 特殊文件

|| 文件 | 用途 |
||------|---------|
|| `app.vue` | 根组件（有 pages/ 时可选） |
|| `app.config.ts` | 运行时应用配置 |
|| `error.vue` | 自定义错误页面 |
|| `nuxt.config.ts` | 构建时配置 |
|| `.nuxtignore` | 从 Nuxt 忽略文件 |
|| `.env` | 环境变量 |

## 文件命名约定

|| 模式 | 含义 |
||---------|---------|
|| `[param]` | 动态路由参数 |
|| `[[param]]` | 可选参数 |
|| `[...slug]` | 捕获全部路由 |
|| `(group)` | 路由组（不在 URL 中） |
|| `.client.vue` | 仅客户端组件 |
|| `.server.vue` | 仅服务器组件 |
|| `.global.ts` | 全局中间件 |

## 最佳实践

### 文件组织原则

1. **按功能分组** - 相关文件放在一起
2. **一致的命名** - 组件使用 PascalCase
3. **避免深层嵌套** - 最多 2-3 层
4. **清晰的命名** - 描述性文件名

### 组件结构

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

interface Props {
  title: string
  count?: number
}

const props = withDefaults(defineProps<Props>(), {
  count: 0,
})

interface Emits {
  (e: 'update', value: number): void
}

const emit = defineEmits<Emits>()
const isActive = ref(false)
const displayCount = computed(() => props.count + 1)

const handleClick = () => {
  isActive.value = !isActive.value
  emit('update', props.count)
}
</script>

<template>
  <div :class="{ active: isActive }" @click="handleClick">
    {{ title }}: {{ displayCount }}
  </div>
</template>
```

### Composables 结构

```ts
export const useCounter = (initialValue = 0) => {
  const count = ref(initialValue)
  
  const increment = () => {
    count.value++
  }
  
  const decrement = () => {
    count.value--
  }
  
  const reset = () => {
    count.value = initialValue
  }
  
  return {
    count,
    increment,
    decrement,
    reset,
  }
}
```

### 页面结构

```vue
<script setup lang="ts">
// 1. 设置页面元信息
definePageMeta({
  layout: 'default',
  middleware: ['auth'],
})

// 2. 路由信息
const route = useRoute()
const router = useRouter()

// 3. 数据获取
const { data, error, status } = await useFetch(
  `/api/posts/${route.params.id}`
)

// 4. 错误处理
if (error.value) {
  throw createError({
    statusCode: error.value.statusCode,
    message: error.value.message,
  })
}
</script>

<template>
  <div>
    <h1>{{ data?.title }}</h1>
    <p>{{ data?.content }}</p>
  </div>
</template>
```

## 常见问题

### 组件未自动导入

**原因：** 组件不在 `components/` 目录下

**解决：**

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  components: [
    {
      path: '~/custom-components',
      pathPrefix: false,
    },
  ],
})
```

### Composables 未自动导入

**原因：** 嵌套在子目录中

**解决：**

```ts
// composables/index.ts
export * from './nested/useHelper'
```

## 文件大小和复杂度

### 推荐限制

- 单个组件文件：最多 300 行
- 单个 composable：最多 200 行
- 单个页面：最多 400 行
- 嵌套深度：最多 3 层

<!-- 
源码参考：
- https://nuxt.com/docs/directory-structure
- https://nuxt.com/docs/directory-structure/app
- https://nuxt.com/docs/directory-structure/server
-->
