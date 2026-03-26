---
name: icons
description: 在幻灯片中使用开源图标
---

# 图标

直接在 markdown 中使用任何开源图标。由 unplugin-icons 和 Iconify 提供支持。

## 安装

```bash
pnpm add @iconify-json/[collection-name]
```

## 用法

使用组件语法 `<collection-icon-name />`：

```md
<mdi-account-circle />
<carbon-badge />
<uim-rocket />
<logos-vue />
```

## 热门图标集

- `@iconify-json/mdi` - Material Design 图标
- `@iconify-json/carbon` - Carbon Design
- `@iconify-json/logos` - SVG 徽标
- `@iconify-json/twemoji` - Twitter 表情符号

## 样式

像任何 HTML 元素一样设置样式：

```html
<uim-rocket class="text-3xl text-red-400 mx-2" />
<uim-rocket class="text-3xl text-orange-400 animate-ping" />
```

## 浏览图标

- https://icones.js.org/
- https://icon-sets.iconify.design/
