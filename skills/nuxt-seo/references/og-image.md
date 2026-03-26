# OG 图像生成

使用 Vue 组件动态生成 Open Graph 图像。

## 快速开始

```ts
// 组件优先(推荐)
defineOgImage('NuxtSeo', { title: '我的页面标题' })

// 对象语法
defineOgImage({ component: 'NuxtSeo', title: '我的页面标题' })

// 禁用 OG 图像
defineOgImage(false)
```

## 内置模板

`NuxtSeo` 模板支持:

```ts
defineOgImage('NuxtSeo', {
  title: 'Hello World',
  description: '我的描述',
  theme: '#3b82f6',
  colorMode: 'dark',
  icon: 'carbon:cloud',
  siteName: '我的站点',
  siteLogo: '/logo.png'
})
```

## 每页多图像

使用 `key` 获取特定平台的图像:

```ts
// 默认 OG 图像 (1200x600)
defineOgImage('NuxtSeo', { title: '默认' })

// WhatsApp 方形 (800x800)
defineOgImage('NuxtSeo', {
  title: '方形',
  key: 'square',
  width: 800,
  height: 800
})
```

## 自定义 Vue 组件

在 `components/OgImage/` 中创建:

```vue
<!-- components/OgImage/Blog.vue -->
<script setup lang="ts">
defineProps<{ title: string; author: string }>()
</script>

<template>
  <div class="w-full h-full flex flex-col justify-center items-center bg-gradient-to-br from-blue-500 to-purple-600 p-12">
    <h1 class="text-6xl font-bold text-white text-center">{{ title }}</h1>
    <p class="text-2xl text-white/80 mt-4">By {{ author }}</p>
  </div>
</template>
```

在页面中使用:

```ts
defineOgImage('OgImageBlog', { title: '我的文章', author: 'John' })
```

## 渲染器

| 渲染器 | 速度 | CSS 支持 | Edge | 最适合                   |
| -------- | ----- | ----------- | ---- | -------------------------- |
| satori   | 快  | 部分     | ✅   | 默认,大多数模板    |
| chromium | 慢  | 完整        | ❌   | 复杂设计,预渲染 |

```ts
export default defineNuxtConfig({
  ogImage: {
    defaults: { renderer: 'satori' }
  }
})
```

### Satori 限制

- 无 `display: grid` - 使用 `flex`
- 无显式尺寸的 `position: absolute`
- 字体: 使用 `@nuxt/fonts` 并设置 `global: true` 以获得最佳效果

## 配置

```ts
export default defineNuxtConfig({
  ogImage: {
    defaults: {
      component: 'NuxtSeo',
      width: 1200,
      height: 600,
      cacheMaxAgeSeconds: 60 * 60 * 24 * 3  // 3 天
    },
    // 对于静态站点
    zeroRuntime: true
  }
})
```

## Nuxt Content

Frontmatter:

```yaml
---
ogImage:
  component: OgImageBlog
  props:
    author: John Doe
---
```

使用 `asSeoCollection()` (参见主 SKILL.md):

```vue
<script setup>
const { data: page } = await useAsyncData(() => queryCollection('posts').path(route.path).first())
if (page.value?.ogImage)
  defineOgImage(page.value.ogImage)
</script>
```

## 调试

- 预览: `/__og-image__/image/[path]/og.png`
- 检查器: 在配置中启用 `ogImage: { debug: true }`

## 截图

捕获页面作为 OG 图像(需要 Chromium):

```ts
defineOgImageScreenshot({
  colorScheme: 'dark',
  mask: '.navigation, .footer',
  selector: '.article-content'
})
```

## 路由规则

```ts
export default defineNuxtConfig({
  routeRules: {
    '/blog/**': { ogImage: { component: 'OgImageBlog' } },
    '/admin/**': { ogImage: false }
  }
})
```

## 部署

社区模板仅用于开发。部署前,请弹出:

```bash
npx nuxt-og-image eject NuxtSeo
```
