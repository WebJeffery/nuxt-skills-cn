---
name: "element-plus-quickstart"
description: "帮助在 Vue 3 项目中设置和配置 Element Plus。当用户需要在新项目或现有项目中安装、导入和配置 Element Plus 时调用。"
---

# Element Plus 快速开始

本技能提供了在 Vue 3 项目中设置 Element Plus 全面指导，包括安装、导入方法和全局配置。

## 何时调用

在以下情况下调用此技能：
- 用户想要在 Vue 3 项目中安装 Element Plus
- 用户需要完整导入与按需导入帮助
- 用户询问如何使用 Vite、Webpack  Nuxt 配置 Element Plus
- 用户需要设置全局配置（size、zIndex）
- 用户遇到 Element Plus 安装设置问题

## 安装方法

### 包管理器安装

```bash
# npm
npm install element-plus --save

# yarn
yarn add element-plus

# pnpm
pnpm install element-plus
```

### 浏览器导入（CDN）

```html
<head>
  <!-- 导入样式 -->
  <link rel="stylesheet" href="//unpkg.com/element-plus/dist/index.css" />
  <!-- 导入 Vue 3 -->
  <script src="//unpkg.com/vue@3"></script>
  <!-- 导入组件库 -->
  <script src="//unpkg.com/element-plus"></script>
</head>
```

## 导入方法

### 完整导入

适用于包大小不是主要关注项目。

```ts
import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import App from './App.vue'

const app = createApp(App)
app.use(ElementPlus)
app.mount('#app')
```

#### Volar 支持

在 `tsconfig.json` 中添加全局组件类型定义：

```json
{
  "compilerOptions": {
    "types": ["element-plus/global"]
  }
}
```

### 按需导入（推荐）

安装所需要插件：

```bash
npm install -D unplugin-vue-components unplugin-auto-import
```

#### Vite 配置

```ts
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

export default defineConfig({
  plugins: [
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
})
```

#### Webpack 配置

```js
const AutoImport = require('unplugin-auto-import/webpack')
const Components = require('unplugin-vue-components/webpack')
const { ElementPlusResolver } = require('unplugin-vue-components/resolvers')

module.exports = {
  plugins: [
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
}
```

### Nuxt 集成

安装 Nuxt 模块：

```bash
npm install -D @element-plus/nuxt
```

添加到 Nuxt 配置：

```ts
export default defineNuxtConfig({
  modules: ['@element-plus/nuxt'],
})
```

## 全局配置

### 完整导入配置

```ts
import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import App from './App.vue'

const app = createApp(App)
app.use(ElementPlus, { size: 'small', zIndex: 3000 })
```

### 按需导入配置

使用 `el-config-provider`：

```vue
<template>
  <el-config-provider :size="size" :z-index="zIndex">
    <app />
  </el-config-provider>
</template>

<script setup>
import { ref } from 'vue'

const zIndex = ref(3000)
const size = ref('small')
</script>
```

## 浏览器兼容性

| 版本 | Chrome | Edge | Firefox | Safari |
|---------|--------|------|---------|--------|
| < 2.5.0 | ≥ 64   | ≥ 79 | ≥ 78    | ≥ 12   |
| 2.5.0+  | ≥ 85   | ≥ 85 | ≥ 79    | ≥ 14.1 |

**注意**：由于 Vue 3 不再支持 IE11，Element Plus 也不支持 IE11。

## Sass 配置

对于 2.8.5+ 版本，最低兼容 Sass 版本是 1.79.0。

要解决遗留 JS API 弃用警告，请在 `vite.config.ts` 中配置：

```ts
css: {
  preprocessorOptions: {
    scss: { api: 'modern-compiler' },
  }
}
```

## 入门模板

- [Vite 模板](https://github.com/element-plus/element-plus-vite-starter)
- [Nuxt 模板](https://github.com/element-plus/element-plus-nuxt-starter)
- [Laravel 模板](https://github.com/element-plus/element-plus-in-laravel-starter)

## 常见问题

### DOM 模板中双闭合标签

在 DOM 模板中使用 Element Plus 时，使用双闭合标签以避免解析问题：

```html
<!-- 正确 -->
<el-table>
  <el-table-column></el-table-column>
</el-table>

<!-- 错误 -->
<el-table>
  <el-table-column />
</el-table>
```

## 输入参数

使用此技能时，提供以下信息：
- **项目类型**：Vite、Webpack、Nuxt  CDN
- **导入方法**：完整导入按需导入（推荐）
- **配置需求**：Size、zIndex 其他全局设置
- **TypeScript 使用**：项目是否使用 TypeScript

## 输出格式

此技能提供：
1. 指定包管理器安装命令
2. 构建工具配置代码片段
3. 全局配置示例
4. 常见设置问题故障排除提示

## 使用限制

- 需要 Vue 3.x（不支持 Vue 2）
- 不支持 IE11
- 对于 SSR 项目，可能需要额外配置（请参阅 SSR 技能）
- 不建议在生产应用程序中使用 CDN
