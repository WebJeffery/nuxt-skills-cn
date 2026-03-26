---
name: vitepress-configuration
description: 配置文件设置、defineConfig 助手、站点元数据和构建选项
---

# 配置

VitePress 配置定义在 `.vitepress/config.[js|ts|mjs|mts]` 中。使用 `defineConfig` 以获得 TypeScript 智能感知。

## 基本配置

```ts
// .vitepress/config.ts
import { defineConfig } from 'vitepress'

export default defineConfig({
  // 站点元数据
  title: 'My Docs',
  description: 'Documentation site',
  lang: 'en-US',
  
  // URL 基础路径(对于 GitHub Pages: '/repo-name/')
  base: '/',
  
  // 主题配置
  themeConfig: {
    // 参见 theme-config.md
  }
})
```

## 站点元数据

```ts
export default defineConfig({
  title: 'VitePress',           // 显示在导航中,用于页面标题
  titleTemplate: ':title - Docs', // 页面标题格式(:title = h1)
  description: 'Site description', // Meta 描述
  lang: 'en-US',                 // HTML lang 属性
  
  head: [
    ['link', { rel: 'icon', href: '/favicon.ico' }],
    ['meta', { name: 'theme-color', content: '#5f67ee' }],
    ['script', { async: '', src: 'https://analytics.example.com/script.js' }]
  ]
})
```

## 构建选项

```ts
export default defineConfig({
  // 源文件目录(相对于项目根目录)
  srcDir: './src',
  
  // 从源中排除的模式
  srcExclude: ['**/README.md', '**/TODO.md'],
  
  // 输出目录
  outDir: './.vitepress/dist',
  
  // 缓存目录
  cacheDir: './.vitepress/cache',
  
  // 没有 .html 扩展名的干净 URL(需要服务器支持)
  cleanUrls: true,
  
  // 在构建期间忽略死链接
  ignoreDeadLinks: true,
  // 或特定模式:
  ignoreDeadLinks: ['/playground', /^https?:\/\/localhost/],
  
  // 从 git 获取最后更新时间戳
  lastUpdated: true
})
```

## 路由重写

将源路径映射到不同的输出路径:

```ts
export default defineConfig({
  rewrites: {
    // 静态映射
    'packages/pkg-a/src/index.md': 'pkg-a/index.md',
    
    // 动态参数
    'packages/:pkg/src/:slug*': ':pkg/:slug*'
  }
})
```

## 外观(暗色模式)

```ts
export default defineConfig({
  appearance: true,           // 启用切换(默认)
  appearance: 'dark',         // 默认为暗色
  appearance: 'force-dark',   // 始终为暗色,无切换
  appearance: 'force-auto',   // 始终跟随系统首选项
  appearance: false           // 禁用暗色模式
})
```

## Vite 和 Vue 配置

```ts
export default defineConfig({
  // 将选项传递给 Vite
  vite: {
    plugins: [],
    resolve: { alias: {} },
    css: { preprocessorOptions: {} }
  },
  
  // 将选项传递给 @vitejs/plugin-vue
  vue: {
    template: { compilerOptions: {} }
  },
  
  // 配置 markdown-it
  markdown: {
    lineNumbers: true,
    toc: { level: [1, 2, 3] },
    math: true, // 需要 markdown-it-mathjax3
    container: {
      tipLabel: 'TIP',
      warningLabel: 'WARNING',
      dangerLabel: 'DANGER'
    }
  }
})
```

## 构建钩子

```ts
export default defineConfig({
  // 转换页面数据
  transformPageData(pageData, { siteConfig }) {
    pageData.frontmatter.head ??= []
    pageData.frontmatter.head.push([
      'meta', { name: 'og:title', content: pageData.title }
    ])
  },
  
  // 在生成每个页面之前转换 head
  async transformHead(context) {
    return [['meta', { name: 'custom', content: context.page }]]
  },
  
  // 构建完成后
  async buildEnd(siteConfig) {
    // 生成 sitemap、RSS 等
  }
})
```

## 动态配置

对于异步配置:

```ts
export default async () => {
  const data = await fetch('https://api.example.com/data').then(r => r.json())
  
  return defineConfig({
    title: data.title,
    themeConfig: {
      sidebar: data.sidebar
    }
  })
}
```

## 关键点

- 配置文件支持 `.js`、`.ts`、`.mjs`、`.mts` 扩展名
- 使用 `defineConfig` 以获得 TypeScript 支持
- `base` 必须以 `/` 开头和结尾以进行子路径部署
- `srcDir` 将源文件与项目根目录分离
- 构建钩子启用自定义转换和后处理

<!--
Source references:
- https://vitepress.dev/reference/site-config
- https://vitepress.dev/guide/getting-started
-->
