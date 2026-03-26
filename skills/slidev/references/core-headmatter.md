---
name: headmatter
description: 第一个 frontmatter 块中的幻灯片组级别配置选项
---

# Headmatter 配置

第一个 frontmatter 块中的幻灯片组级别配置选项。

## 主题与外观

```yaml
---
theme: default              # 主题包或路径
colorSchema: auto           # 'auto' | 'light' | 'dark'
favicon: /favicon.ico       # 网站 图标 URL
aspectRatio: 16/9           # 幻灯片宽高比
canvasWidth: 980            # 画布宽度（像素）
---
```

## 字体

```yaml
---
fonts:
  sans: Roboto
  serif: Roboto Slab
  mono: Fira Code
  provider: google          # 'google' | 'none'
---
```

## 代码与高亮

```yaml
---
highlighter: shiki          # 代码高亮器
lineNumbers: false          # 显示行号
monaco: true                # 启用 Monaco 编辑器（'true' | 'dev' | 'build'）
twoslash: true              # 启用 TwoSlash
monacoTypesSource: local    # 'local' | 'cdn' | 'none'
---
```

## 功能

```yaml
---
drawings:
  enabled: true             # 启用绘图模式
  persist: false            # 保存绘图
  presenterOnly: false      # 仅演讲者可以绘图
  syncAll: true             # 在实例间同步
record: dev                 # 启用录制
selectable: true            # 文本选择
contextMenu: true           # 右键菜单
wakeLock: true              # 防止屏幕休眠
---
```

## 导出与构建

```yaml
---
download: false             # PDF 下载按钮
exportFilename: slides      # 导出文件名
export:
  format: pdf
  timeout: 30000
  withClicks: false
  withToc: false
---
```

## 信息与 SEO

```yaml
---
title: 我的演示
titleTemplate: '%s - Slidev'
author: 您的姓名
keywords: slidev, presentation
info: |
  ## 关于
  演示文稿描述
---
```

## SEO 元标签

```yaml
---
seoMeta:
  ogTitle: 演示文稿标题
  ogDescription: 描述
  ogImage: https://example.com/og.png
  ogUrl: https://example.com
  twitterCard: summary_large_image
  twitterTitle: 标题
  twitterDescription: 描述
  twitterImage: https://example.com/twitter.png
---
```

## 插件与主题

```yaml
---
theme: seriph
addons:
  - excalidraw
  - '@slidev/plugin-notes'
---
```

## 主题配置

```yaml
---
themeConfig:
  primary: '#5d8392'
  # 主题特定选项
---
```

## 默认值

为所有幻灯片设置默认 frontmatter：

```yaml
---
defaults:
  layout: default
  transition: fade
---
```

## HTML 属性

```yaml
---
htmlAttrs:
  dir: ltr
  lang: en
---
```

## 演讲者与浏览器

```yaml
---
presenter: true             # 'true' | 'dev' | 'build'
browserExporter: dev        # 'true' | 'dev' | 'build'
routerMode: history         # 'history' | 'hash'
---
```

## 远程资源

```yaml
---
remoteAssets: false         # 将远程资源下载到本地
plantUmlServer: https://www.plantuml.com/plantuml
---
```

## 完整模板

```yaml
---
theme: default
title: 演示文稿标题
author: 您的姓名
highlighter: shiki
lineNumbers: true
transition: slide-left
aspectRatio: 16/9
canvasWidth: 980
fonts:
  sans: Roboto
  mono: Fira Code
drawings:
  enabled: true
  persist: true
download: true
---
```