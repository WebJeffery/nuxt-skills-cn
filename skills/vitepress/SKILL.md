---
name: vitepress
description: 基于 Vite 和 Vue 的 VitePress 静态站点生成器。用于构建文档站点、配置主题或使用 Vue 组件编写 Markdown。
---

VitePress 是基于 Vite 和 Vue 3 构建的静态站点生成器(SSG)。它获取 Markdown 内容,应用主题,并生成静态 HTML,该 HTML 成为快速导航的 SPA。非常适合文档、博客和营销站点。

**关键特性:**
- 基于 `.md` 文件的文件路由
- Vue 组件可直接在 Markdown 中工作
- 快速 HMR,即时更新(<100ms)
- 针对文档优化的默认主题
- 内置搜索(本地或 Algolia)

**在使用 VitePress 项目之前:**
- 检查 `.vitepress/config.ts` 以获取站点配置
- 查看 `.vitepress/theme/` 以获取自定义主题扩展
- `public/` 目录包含按原样提供的静态资源

## 核心

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 配置 | 配置文件设置、defineConfig、站点元数据 | [core-config](references/core-config.md) |
| CLI | 命令行界面:dev、build、preview、init | [core-cli](references/core-cli.md) |
| 路由 | 基于文件的路由、源目录、重写 | [core-routing](references/core-routing.md) |
| Markdown | Frontmatter、容器、表格、锚点、包含 | [core-markdown](references/core-markdown.md) |

## 功能

### 代码和内容

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 代码块 | 语法高亮、行高亮、差异、焦点 | [features-code-blocks](references/features-code-blocks.md) |
| Markdown 中的 Vue | 组件、script setup、指令、模板 | [features-vue](references/features-vue.md) |
| 数据加载 | 构建时数据加载器、createContentLoader | [features-data-loading](references/features-data-loading.md) |
| 动态路由 | 从数据生成页面、路径加载器文件 | [features-dynamic-routes](references/features-dynamic-routes.md) |

## 主题

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 主题配置 | 导航、侧边栏、搜索、社交链接、页脚 | [theme-config](references/theme-config.md) |
| 自定义 | CSS 变量、插槽、字体、全局组件 | [theme-customization](references/theme-customization.md) |
| 自定义主题 | 从头开始构建主题、主题接口 | [theme-custom](references/theme-custom.md) |

## 高级

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 国际化 | 多语言站点、区域设置配置 | [advanced-i18n](references/advanced-i18n.md) |
| SSR 兼容性 | 服务端渲染、ClientOnly、动态导入 | [advanced-ssr](references/advanced-ssr.md) |

## 配方

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 部署 | GitHub Pages、Netlify、Vercel、Cloudflare、Nginx | [recipes-deploy](references/recipes-deploy.md) |
