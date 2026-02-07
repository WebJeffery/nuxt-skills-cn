---
title: 在通用 SSR 代码中保护平台特定 API
impact: HIGH
impactDescription: 在服务器上访问仅浏览器 API 会导致崩溃；Node.js API 在浏览器中失败
type: gotcha
tags: [vue3, ssr, browser-api, nodejs, universal, isomorphic, server-side-rendering]
---

# 在通用 SSR 代码中保护平台特定 API

**影响：高** - SSR 应用程序在服务器（Node.js）和客户端（浏览器）上运行相同的代码。浏览器 API 如 `window`、`document` 和 `localStorage` 在 Node.js 中不存在，将抛出 `ReferenceError`。同样，Node.js API 如 `fs` 和 `process` 在浏览器中不可用。

通用/同构代码必须保护平台特定 API 访问或使用在两个平台上都可以工作的库。

## 任务清单

- [ ] 永远不要在 `setup()` 或 `created()` 中访问 `window`、`document`、`navigator`
- [ ] 将浏览器 API 访问移动到 `onMounted()` 生命周期钩子
- [ ] 在生命周期之外需要时使用 `typeof window !== 'undefined'` 保护
- [ ] 对常见功能使用跨平台库（fetch、storage）
- [ ] 在 Nuxt 项目中使用 Nuxt 的 `process.client` / `process.server` 保护

## 会破坏 SSR 的常见浏览器 API

| API | Node.js 行为 |
|-----|-----------------|
| `window` | `ReferenceError: window is not defined` |
| `document` | `ReferenceError: document is not defined` |
| `localStorage` / `sessionStorage` | `ReferenceError` |
| `navigator` | `ReferenceError` |
| `location` | `ReferenceError` |
| `history` | `ReferenceError` |
| `alert` / `confirm` / `prompt` | `ReferenceError` |
| `requestAnimationFrame` | `ReferenceError` |
| `IntersectionObserver` | `ReferenceError` |
| `ResizeObserver` | `ReferenceError` |

**不正确 - 在服务器上崩溃：**
```javascript
// 错误：这些在 setup/SSR 期间运行 - 在 Node.js 中崩溃
const width = ref(window.innerWidth)
const theme = localStorage.getItem('theme')
const userAgent = navigator.userAgent
```

```vue
<script setup>
import { ref } from 'vue'

// 错误：在服务器上运行，崩溃
const scrollY = ref(window.scrollY)

// 错误：document 在服务器上不存在
document.title = 'My Page'
</script>
```

**正确 - 使用 onMounted：**
```vue
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

// 在服务器上工作的安全默认值
const width = ref(0)
const theme = ref('light')
const scrollY = ref(0)

onMounted(() => {
  // 浏览器 API 仅在挂载后访问（仅客户端）
  width.value = window.innerWidth
  theme.value = localStorage.getItem('theme') || 'light'
  scrollY.value = window.scrollY

  // 事件监听器在 mounted 中是安全的
  window.addEventListener('resize', handleResize)
  window.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
  window.removeEventListener('scroll', handleScroll)
})

function handleResize() {
  width.value = window.innerWidth
}

function handleScroll() {
  scrollY.value = window.scrollY
}
</script>
```

**正确 - 使用 typeof 保护：**
```javascript
// 当你需要在生命周期钩子之外检查时
function getStoredValue(key, defaultValue) {
  if (typeof window !== 'undefined' && window.localStorage) {
    return localStorage.getItem(key) ?? defaultValue
  }
  return defaultValue
}

// 具有 SSR 感知的组合式函数
export function useMediaQuery(query) {
  const matches = ref(false)

  // 仅在客户端运行
  if (typeof window !== 'undefined') {
    const mediaQuery = window.matchMedia(query)
    matches.value = mediaQuery.matches

    // 在生命周期中设置监听器
    onMounted(() => {
      const handler = (e) => { matches.value = e.matches }
      mediaQuery.addEventListener('change', handler)
      onUnmounted(() => mediaQuery.removeEventListener('change', handler))
    })
  }

  return matches
}
```

## Nuxt.js 保护

```vue
<script setup>
// Nuxt 提供 process.client 和 process.server
if (process.client) {
  // 仅在浏览器中运行
  window.analytics.track('page_view')
}

if (process.server) {
  // 仅在服务器上运行
  console.log('Rendering on server')
}
</script>
```

```vue
<template>
  <!-- ClientOnly 组件用于仅客户端渲染 -->
  <ClientOnly>
    <BrowserOnlyChart :data="chartData" />
    <template #fallback>
      <ChartSkeleton />
    </template>
  </ClientOnly>
</template>
```

## 跨平台库

使用抽象平台差异的库：

```javascript
// Fetch - 在 Node.js 18+ 和浏览器中都可以工作
const response = await fetch('/api/data')

// 对于较旧的 Node.js，使用 node-fetch 或 axios
import axios from 'axios'
const { data } = await axios.get('/api/data')
```

```javascript
// 通用 cookie 处理
import Cookies from 'js-cookie' // 仅客户端
import { parse } from 'cookie' // 两者都可以工作

// 在 Nuxt 中，使用 useCookie()
const token = useCookie('auth-token')
```

## 在浏览器中会破坏的常见 Node.js API

| API | 浏览器行为 |
|-----|-----------------|
| `fs` | Module not found |
| `path` | Module not found |
| `process`（完整）| Undefined 或受限 |
| `Buffer` | Undefined（除非 polyfilled）|
| `__dirname` / `__filename` | Undefined |
| `require()` | 在 ES 模块中 Undefined |

**不正确：**
```javascript
// 错误：通用代码中的 Node.js API
import fs from 'fs'
const config = JSON.parse(fs.readFileSync('./config.json'))
```

**正确 - 分离服务器代码：**
```javascript
// server/utils.js - 仅服务器文件
import fs from 'fs'
export function loadConfig() {
  return JSON.parse(fs.readFileSync('./config.json'))
}

// app.js - 通用代码使用 API 代替
const config = await fetch('/api/config').then(r => r.json())
```

## 环境检测实用程序

```javascript
// utils/environment.js
export const isClient = typeof window !== 'undefined'
export const isServer = !isClient

export const isBrowser = isClient && typeof document !== 'undefined'
export const isNode = typeof process !== 'undefined' &&
                      process.versions?.node != null

// 使用
import { isClient, isServer } from '@/utils/environment'

if (isClient) {
  // 浏览器特定代码
}
```

## 第三方库问题

某些库在导入时自动访问浏览器 API：

```javascript
// 错误：库在导入时访问 window
import SomeChartLibrary from 'some-chart-library'
// ^ 如果库执行：const x = window.something，则在服务器上崩溃
```

**正确 - 动态导入：**
```vue
<script setup>
import { defineAsyncComponent } from 'vue'

// 动态导入仅在客户端加载
const Chart = defineAsyncComponent(() =>
  import('some-chart-library').then(m => m.ChartComponent)
)
</script>

<template>
  <ClientOnly>
    <Chart :data="data" />
  </ClientOnly>
</template>
```

## 参考
- [Vue.js SSR - 平台特定 API](https://vuejs.org/guide/scaling-up/ssr.html#access-to-platform-specific-apis)
- [Nuxt ClientOnly 组件](https://nuxt.com/docs/api/components/client-only)
- [MDN: Web APIs](https://developer.mozilla.org/en-US/docs/Web/API)
