---
name: vitepress-i18n
description: 多语言支持、翻译配置、语言切换和本地化
---

# 国际化 (i18n)

VitePress 提供内置的 i18n 支持以创建多语言站点。

## 基本配置

在站点配置中配置语言:

```ts
// .vitepress/config.ts
import { defineConfig } from 'vitepress'

export default defineConfig({
  // 默认语言
  lang: 'en-US',
  
  title: 'My Site',
  description: 'A great site',
  
  // 多语言配置
  locales: {
    root: {
      label: 'English',
      lang: 'en-US',
      title: 'My Site',
      description: 'A great site',
      themeConfig: {
        nav: [
          { text: 'Guide', link: '/guide/' }
        ]
      }
    },
    'zh-CN': {
      label: '简体中文',
      lang: 'zh-CN',
      title: '我的站点',
      description: '一个很棒的站点',
      themeConfig: {
        nav: [
          { text: '指南', link: '/zh-CN/guide/' }
        ]
      }
    }
  }
})
```

## 目录结构

为每种语言创建单独的目录:

```
docs/
├── index.md                 # 根语言(英语)
├── guide/
│   ├── index.md
│   └── getting-started.md
└── zh-CN/
    ├── index.md             # 中文主页
    └── guide/
        ├── index.md
        └── getting-started.md
```

## 语言切换器

VitePress 自动在导航栏中添加语言切换器。

自定义标签:

```ts
locales: {
  root: {
    label: 'English',  // 切换器中显示的文本
    // ...
  },
  'zh-CN': {
    label: '简体中文',
    // ...
  }
}
```

## 翻译内容

### 页面翻译

为每种语言创建单独的 markdown 文件:

```md
<!-- guide/getting-started.md -->
# Getting Started

Welcome to the guide.
```

```md
<!-- zh-CN/guide/getting-started.md -->
# 快速开始

欢迎来到指南。
```

### 主题配置翻译

每种语言的独立主题配置:

```ts
locales: {
  root: {
    themeConfig: {
      nav: [
        { text: 'Guide', link: '/guide/' },
        { text: 'API', link: '/api/' }
      ],
      sidebar: {
        '/guide/': [
          { text: 'Introduction', link: '/guide/' },
          { text: 'Getting Started', link: '/guide/getting-started' }
        ]
      }
    }
  },
  'zh-CN': {
    themeConfig: {
      nav: [
        { text: '指南', link: '/zh-CN/guide/' },
        { text: 'API', link: '/zh-CN/api/' }
      ],
      sidebar: {
        '/zh-CN/guide/': [
          { text: '介绍', link: '/zh-CN/guide/' },
          { text: '快速开始', link: '/zh-CN/guide/getting-started' }
        ]
      }
    }
  }
}
```

### 默认主题文本

覆盖默认主题文本:

```ts
locales: {
  root: {
    themeConfig: {
      outlineLabel: 'On this page',
      returnToTopLabel: 'Return to top',
      sidebarMenuLabel: 'Menu',
      darkModeSwitchLabel: 'Appearance',
      lightModeSwitchTitle: 'Switch to light theme',
      darkModeSwitchTitle: 'Switch to dark theme',
      docFooter: {
        prev: 'Previous page',
        next: 'Next page'
      }
    }
  },
  'zh-CN': {
    themeConfig: {
      outlineLabel: '本页目录',
      returnToTopLabel: '返回顶部',
      sidebarMenuLabel: '菜单',
      darkModeSwitchLabel: '外观',
      lightModeSwitchTitle: '切换到浅色模式',
      darkModeSwitchTitle: '切换到深色模式',
      docFooter: {
        prev: '上一页',
        next: '下一页'
      }
    }
  }
}
```

## 链接到翻译页面

使用相对链接,VitePress 会自动处理语言前缀:

```md
<!-- 在 guide/getting-started.md 中 -->
[API Reference](/api/config)  → /api/config (英语)
```

```md
<!-- 在 zh-CN/guide/getting-started.md 中 -->
[API 参考](/api/config)  → /zh-CN/api/config (中文)
```

## 动态内容翻译

在组件中使用 `useData`:

```vue
<script setup>
import { useData } from 'vitepress'

const { lang } = useData()

const messages = {
  'en-US': {
    welcome: 'Welcome',
    goodbye: 'Goodbye'
  },
  'zh-CN': {
    welcome: '欢迎',
    goodbye: '再见'
  }
}

const t = messages[lang.value] || messages['en-US']
</script>

<template>
  <div>
    <p>{{ t.welcome }}</p>
    <p>{{ t.goodbye }}</p>
  </div>
</template>
```

## 使用 i18n 库

集成 vue-i18n:

```ts
// .vitepress/theme/index.ts
import { createI18n } from 'vue-i18n'
import DefaultTheme from 'vitepress/theme'

const i18n = createI18n({
  legacy: false,
  locale: 'en',
  messages: {
    en: {
      hello: 'Hello'
    },
    zh: {
      hello: '你好'
    }
  }
})

export default {
  extends: DefaultTheme,
  enhanceApp({ app }) {
    app.use(i18n)
  }
}
```

```vue
<script setup>
import { useI18n } from 'vue-i18n'

const { t } = useI18n()
</script>

<template>
  <p>{{ t('hello') }}</p>
</template>
```

## SEO 优化

每种语言的 `<html lang>` 自动设置。

自定义 `<head>`:

```ts
export default {
  transformHead({ page }) {
    return [
      ['meta', { property: 'og:locale', content: page.lang }]
    ]
  }
}
```

## 生成站点地图

使用 vitepress-plugin-sitemap:

```ts
// .vitepress/config.ts
import { defineConfig } from 'vitepress'
import { withSitemap } from '@sveltepress/vitepress-plugin-sitemap'

export default withSitemap(
  defineConfig({
    // ...
    sitemap: {
      hostname: 'https://example.com'
    }
  })
)
```

## 关键点

- 在 `locales` 中配置每种语言
- 为每种语言创建单独的目录
- 每种语言的独立主题配置
- 相对链接自动处理语言前缀
- 使用 `useData().lang` 检测当前语言
- 覆盖默认主题文本以进行本地化

<!--
Source references:
- https://vitepress.dev/guide/i18n
-->
