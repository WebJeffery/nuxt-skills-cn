---
name: building-custom-vitepress-theme
description: 创建自定义主题,包括布局、组件和主题配置
---

# 构建自定义主题

从零开始创建自定义主题,而不是扩展默认主题。

## 主题入口文件

创建 `.vitepress/theme/index.ts`:

```ts
// .vitepress/theme/index.ts
import Layout from './Layout.vue'
import './custom.css'

export default {
  Layout,
  enhanceApp({ app, router, siteData }) {
    // 注册全局组件
    app.component('MyComponent', MyComponent)
  }
}
```

## 布局组件

```vue
<!-- .vitepress/theme/Layout.vue -->
<script setup>
import { useData } from 'vitepress'

const { page, frontmatter } = useData()
</script>

<template>
  <div class="layout">
    <header>
      <a href="/" class="home-link">{{ siteData.title }}</a>
      <nav>
        <a v-for="link in siteData.themeConfig.nav" :href="link.link">
          {{ link.text }}
        </a>
      </nav>
    </header>
    
    <main>
      <Content />
    </main>
    
    <footer>
      © {{ new Date().getFullYear() }} {{ siteData.title }}
    </footer>
  </div>
</template>

<style scoped>
.layout {
  max-width: 1000px;
  margin: 0 auto;
  padding: 20px;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 0;
}

nav a {
  margin-left: 20px;
}
</style>
```

## 使用默认主题组件

导入特定组件:

```vue
<!-- .vitepress/theme/Layout.vue -->
<script setup>
import { useData } from 'vitepress'
import VPNavBar from 'vitepress/theme-default/components/VPNavBar.vue'
import VPFooter from 'vitepress/theme-default/components/VPFooter.vue'

const { siteData } = useData()
</script>

<template>
  <div class="layout">
    <VPNavBar />
    <main><Content /></main>
    <VPFooter />
  </div>
</template>
```

## 响应式布局

```vue
<script setup>
import { useData } from 'vitepress'
import { ref, onMounted, onUnmounted } from 'vue'

const { page } = useData()
const isMobile = ref(false)

const checkMobile = () => {
  isMobile.value = window.innerWidth < 768
}

onMounted(() => {
  checkMobile()
  window.addEventListener('resize', checkMobile)
})

onUnmounted(() => {
  window.removeEventListener('resize', checkMobile)
})
</script>

<template>
  <div :class="{ 'mobile': isMobile }">
    <Content />
  </div>
</template>
```

## 暗色模式支持

```vue
<script setup>
import { useData } from 'vitepress'
import { onMounted, watch } from 'vue'

const { isDark } = useData()

const toggleTheme = () => {
  isDark.value = !isDark.value
}

onMounted(() => {
  // 从 localStorage 或系统偏好初始化
  const saved = localStorage.getItem('vitepress-theme-appearance')
  if (saved) {
    isDark.value = saved === 'dark'
  } else if (window.matchMedia('(prefers-color-scheme: dark)').matches) {
    isDark.value = true
  }
})

watch(isDark, (value) => {
  document.documentElement.classList.toggle('dark', value)
  localStorage.setItem('vitepress-theme-appearance', value ? 'dark' : 'light')
})
</script>

<template>
  <button @click="toggleTheme">
    {{ isDark ? 'Light' : 'Dark' }}
  </button>
</template>
```

## 自定义 404 页面

```vue
<!-- .vitepress/theme/NotFound.vue -->
<script setup>
import { useData } from 'vitepress'

const { siteData } = useData()
</script>

<template>
  <div class="not-found">
    <h1>404</h1>
    <p>Page not found</p>
    <a href="/">Go home</a>
  </div>
</template>

<style scoped>
.not-found {
  text-align: center;
  padding: 100px 20px;
}
</style>
```

在主题入口中注册:

```ts
// .vitepress/theme/index.ts
import Layout from './Layout.vue'
import NotFound from './NotFound.vue'

export default {
  Layout,
  NotFound,
  enhanceApp({ app }) {
    // ...
  }
}
```

## 主题配置类型

```ts
// .vitepress/theme/index.ts
import type { Theme } from 'vitepress'

const theme: Theme = {
  Layout,
  NotFound,
  enhanceApp({ app, router, siteData }) {
    app.provide('theme-config', siteData.themeConfig)
  }
}

export default theme
```

## 使用插件

```ts
// .vitepress/theme/index.ts
import Layout from './Layout.vue'
import mediumZoom from 'medium-zoom'

export default {
  Layout,
  enhanceApp({ app }) {
    app.mount('#app')
    
    // 初始化插件
    mediumZoom('img:not([no-zoom])')
  }
}
```

## CSS 架构

```css
/* .vitepress/theme/custom.css */
:root {
  /* 颜色变量 */
  --c-primary: #42b883;
  --c-text: #2c3e50;
  --c-bg: #ffffff;
  
  /* 间距 */
  --spacing-xs: 4px;
  --spacing-sm: 8px;
  --spacing-md: 16px;
  --spacing-lg: 24px;
  
  /* 字体 */
  --font-sans: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  --font-mono: 'Fira Code', monospace;
}

.dark {
  --c-text: #e2e8f0;
  --c-bg: #1a202c;
}

/* 基础样式 */
body {
  font-family: var(--font-sans);
  color: var(--c-text);
  background: var(--c-bg);
}

/* 组件样式 */
.btn {
  padding: var(--spacing-sm) var(--spacing-md);
  background: var(--c-primary);
  color: white;
  border-radius: 4px;
}
```

## 关键点

- 自定义主题导出 `Layout`、`NotFound` 和 `enhanceApp`
- `Content` 组件渲染 markdown 内容
- 使用 `useData()` 访问页面和站点数据
- 在 `enhanceApp` 中注册全局组件
- `NotFound` 组件用于 404 页面
- 使用 CSS 变量实现主题化和暗色模式

<!--
Source references:
- https://vitepress.dev/guide/custom-theme
-->
