---
name: "element-plus-ssr"
description: "为服务端渲染（SSR）应用程序配置 Element Plus。当用户需要在 SSR 中实现 Element Plus 或修复水合错误时调用。"
---

# Element Plus 服务端渲染（SSR）

本技能提供了在 SSR 环境中使用 Element Plus 全面指导，包括 Nuxt.js 和自定义 SSR 设置。

## 何时调用

在以下情况下调用此技能：
- 用户正在使用 Element Plus 构建 SSR 应用程序
- 用户遇到 Element Plus 组件水合错误
- 用户需要为 Nuxt.js 配置 Element Plus
- 用户询问 SSR 中 Teleport 处理
- 用户需要修复 ID  z-index 水合不匹配

## Nuxt.js 集成（推荐）

对于 Nuxt.js 用户，使用官方 Element Plus Nuxt 模块：

### 安装

```bash
npm install -D @element-plus/nuxt
```

### 配置

添加到 `nuxt.config.ts`：

```ts
export default defineNuxtConfig({
  modules: ['@element-plus/nuxt'],
})
```

该模块自动处理所有 SSR 特定配置。

更多详情请参阅 [Element Plus Nuxt 文档](https://github.com/element-plus/element-plus-nuxt#readme)。

## 自定义 SSR 配置

对于自定义 SSR 设置，你需要处理几个特殊情况以避免水合错误。

### 1. 提供 ID 注入

Element Plus 为组件生成唯一 ID。在 SSR 中，服务器和客户端上不同 ID 会导致水合错误。

**解决方案**：注入 `ID_INJECTION_KEY`：

```ts
import { createApp } from 'vue'
import { ID_INJECTION_KEY } from 'element-plus'
import App from './App.vue'

const app = createApp(App)
app.provide(ID_INJECTION_KEY, {
  prefix: 1024,
  current: 0,
})
```

### 2. 提供 ZIndex

Dialog、Drawer 和 Tooltip 等组件使用 z-index。如果服务器和客户端之间 z-index 值不同，可能会发生水合错误。

**解决方案**：注入 `ZINDEX_INJECTION_KEY`：

```ts
import { createApp } from 'vue'
import { ZINDEX_INJECTION_KEY } from 'element-plus'
import App from './App.vue'

const app = createApp(App)
app.provide(ZINDEX_INJECTION_KEY, { current: 0 })
```

### 3. 处理 Teleports

许多 Element Plus 组件在内部使用 Vue  [Teleport](https://vuejs.org/guide/scaling-up/ssr.html#teleports)：
- ElDialog
- ElDrawer
- ElTooltip
- ElDropdown
- ElSelect
- ElDatePicker
- ElPopover
- ElPopconfirm

#### 选项 A：仅客户端渲染

仅在客户端渲染 Teleport 组件：

**使用 Nuxt  ClientOnly**：

```html
<client-only>
  <el-tooltip content="提示内容">
    <el-button>提示</el-button>
  </el-tooltip>
</client-only>
```

**手动仅客户端**：

```vue
<script setup>
import { ref, onMounted } from 'vue'

const isClient = ref(false)

onMounted(() => {
  isClient.value = true
})
</script>

<template>
  <el-tooltip v-if="isClient" content="提示内容">
    <el-button>提示</el-button>
  </el-tooltip>
</template>
```

#### 选项 B：注入 Teleport 标记

在 Teleport 标记注入到 HTML 模板中正确位置：

**HTML 模板**：

```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <title>Element Plus SSR</title>
    <!--preload-links-->
  </head>
  <body>
    <!--app-teleports-->
    <div id="app"><!--app-html--></div>
    <script type="module" src="/src/entry-client.js"></script>
  </body>
</html>
```

**服务器入口（entry-server.js）**：

```js
import { renderToString } from 'vue/server-renderer'
import { createApp } from './main'

export async function render(url, manifest) {
  const { app } = createApp()

  const ctx = {}
  const html = await renderToString(app, ctx)
  const preloadLinks = renderPreloadLinks(ctx.modules, manifest)
  const teleports = renderTeleports(ctx.teleports)

  return [html, preloadLinks, teleports]
}

function renderTeleports(teleports) {
  if (!teleports) return ''
  return Object.entries(teleports).reduce((all, [key, value]) => {
    if (key.startsWith('#el-popper-container-')) {
      return `${all}<div id="${key.slice(1)}">${value}</div>`
    }
    return all
  }, teleports.body || '')
}
```

**服务端预渲染脚本**：

```js
const [appHtml, preloadLinks, teleports] = await render(url, manifest)

const html = template
  .replace('<!--preload-links-->', preloadLinks)
  .replace('<!--app-html-->', appHtml)
  .replace(/(\n|\r\n)\s*<!--app-teleports-->/, teleports)
```

## 完整 SSR 设置示例

### main.ts

```ts
import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import { ID_INJECTION_KEY, ZINDEX_INJECTION_KEY } from 'element-plus'
import App from './App.vue'

export function createApp() {
  const app = createApp(App)

  // SSR 特定注入
  app.provide(ID_INJECTION_KEY, {
    prefix: 1024,
    current: 0,
  })

  app.provide(ZINDEX_INJECTION_KEY, { current: 0 })

  app.use(ElementPlus)

  return { app }
}
```

### entry-client.ts

```ts
import { createApp } from './main'

const { app } = createApp()

app.mount('#app')
```

### entry-server.ts

```ts
import { renderToString } from 'vue/server-renderer'
import { createApp } from './main'

export async function render() {
  const { app } = createApp()

  const ctx = {}
  const html = await renderToString(app, ctx)

  return { html, teleports: ctx.teleports }
}
```

## 处理自定义命名空间

如果你修改了 [命名空间](https://element-plus.org/en-US/guide/namespace.html) `append-to` 属性，请调整 teleport 容器 ID：

```js
function renderTeleports(teleports) {
  if (!teleports) return ''
  return Object.entries(teleports).reduce((all, [key, value]) => {
    // 如果使用自定义命名空间，请调整前缀
    // 默认：'#el-popper-container-'
    // 自定义：'#your-namespace-popper-container-'
    if (key.startsWith('#el-popper-container-')) {
      return `${all}<div id="${key.slice(1)}">${value}</div>`
    }
    return all
  }, teleports.body || '')
}
```

## 常见 SSR 问题

### 1. 水合不匹配

**症状**：控制台警告水合不匹配。

**原因**：
- 缺少 ID 注入
- 缺少 z-index 注入
- Teleport 组件在服务器上渲染

**解决方案**：
- 添加 ID_INJECTION_KEY
- 添加 ZINDEX_INJECTION_KEY
- 对 Teleport 组件使用 ClientOnly

### 2. 缺少样式

**症状**：初始加载时未应用样式。

**解决方案**：确保提供 CSS 并包含在 HTML 中：

```js
// 在你 SSR 构建配置中
import { createSSRApp } from 'vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
```

### 3. 图标未渲染

**症状**：SSR 输出中缺少图标。

**解决方案**：全局注册图标导入它们：

```ts
import * as ElementPlusIconsVue from '@element-plus/icons-vue'

for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
  app.component(key, component)
}
```

## 最佳实践

### 1. 尽可能使用 Nuxt 模块

对于 Nuxt 项目，始终使用 `@element-plus/nuxt` 以避免手动配置。

### 2. 复杂组件使用仅客户端

对于具有复杂 Teleport 行为组件，使用仅客户端渲染：

```vue
<client-only>
  <el-dialog v-model="visible">
    <!-- 对话框内容 -->
  </el-dialog>
</client-only>
```

### 3. 测试水合

始终在开发中测试 SSR 水合：

```bash
# 构建并测试 SSR
npm run build:ssr
npm run serve:ssr
```

### 4. 避免直接 DOM 访问

不要在 setup  created 钩子中直接访问 DOM：

```vue
<script setup>
// 错误 - 在 SSR 中会失败
const el = document.getElementById('my-element')

// 正确 - 使用 onMounted
onMounted(() => {
  const el = document.getElementById('my-element')
})
</script>
```

## 输入参数

使用此技能时，提供：
- **框架**：Nuxt.js 自定义 SSR
- **问题类型**：水合错误、缺少样式其他
- **受影响组件**：哪些 Element Plus 组件有问题
- **当前设置**：你 SSR 配置

## 输出格式

此技能提供：
1. SSR 特定配置代码
2. 水合错误解决方案
3. Teleport 处理示例
4. 使用 Element Plus  SSR 最佳实践

## 使用限制

- 需要支持 SSR 的 Vue 3.x
- 一些组件在 SSR 中需要特殊处理
- 自定义 SSR 设置需要手动配置
- 建议彻底测试 SSR
- 性能可能因 SSR 实现而异

## 资源

- [Vue SSR 文档](https://vuejs.org/guide/scaling-up/ssr.html)
- [Element Plus Nuxt 模块](https://github.com/element-plus/element-plus-nuxt)
- [Element Plus SSR 入门](https://github.com/element-plus/element-plus-nuxt-starter)
