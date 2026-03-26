---
name: preset-icons
description: 使用 Iconify 的纯 CSS 图标，支持任何图标集
---

# 预设 Icons

将任何图标用作纯 CSS 类，由 Iconify 提供支持。

## 安装

```bash
pnpm add -D @unocss/preset-icons @iconify-json/[collection-name]
```

示例：`@iconify-json/mdi` 用于 Material Design Icons，`@iconify-json/carbon` 用于 Carbon 图标。

```ts
import { defineConfig, presetIcons } from 'unocss'

export default defineConfig({
  presets: [
    presetIcons(),
  ],
})
```

## 用法

两种命名约定：
- `<prefix><collection>-<icon>` → `i-ph-anchor-simple-thin`
- `<prefix><collection>:<icon>` → `i-ph:anchor-simple-thin`

```html
<!-- Phosphor anchor 图标 -->
<div class="i-ph-anchor-simple-thin" />

<!-- Material Design alarm 带颜色 -->
<div class="i-mdi-alarm text-orange-400" />

<!-- 大 Vue logo -->
<div class="i-logos-vue text-3xl" />

<!-- 动态：亮色模式下为 Sun，暗色模式下为 Moon -->
<button class="i-carbon-sun dark:i-carbon-moon" />

<!-- 悬停效果 -->
<div class="i-twemoji-grinning-face-with-smiling-eyes hover:i-twemoji-face-with-tears-of-joy" />
```

在 [icones.js.org](https://icones.js.org/) 或 [Iconify](https://icon-sets.iconify.design/) 浏览图标。

## 图标模式

图标自动在 `mask`（单色）和 `background-img`（彩色）之间选择。

### 强制特定模式

- `?mask` - 渲染为 mask（可通过 `currentColor` 着色）
- `?bg` - 渲染为背景图像（保留原始颜色）

```html
<!-- 原始带颜色 -->
<div class="i-vscode-icons:file-type-light-pnpm" />

<!-- 强制 mask 模式，应用自定义颜色 -->
<div class="i-vscode-icons:file-type-light-pnpm?mask text-red-300" />
```

## 选项

```ts
presetIcons({
  scale: 1.2,              // 相对于字体大小的缩放
  prefix: 'i-',            // 类前缀（默认）
  mode: 'auto',            // 'auto' | 'mask' | 'bg'
  extraProperties: {
    'display': 'inline-block',
    'vertical-align': 'middle',
  },
  warn: true,              // 警告缺失的图标
  autoInstall: true,       // 自动安装缺失的图标集
  cdn: 'https://esm.sh/',  // 用于浏览器使用的 CDN
})
```

## 自定义图标集

### 内联 SVG

```ts
presetIcons({
  collections: {
    custom: {
      circle: '<svg viewBox="0 0 120 120"><circle cx="60" cy="60" r="50"></circle></svg>',
    },
  }
})
```

用法：`<span class="i-custom:circle"></span>`

### 文件系统加载器

```ts
import { FileSystemIconLoader } from '@iconify/utils/lib/loader/node-loaders'

presetIcons({
  collections: {
    'my-icons': FileSystemIconLoader(
      './assets/icons',
      svg => svg.replace(/#fff/, 'currentColor')
    ),
  }
})
```

### 动态导入（浏览器）

```ts
import presetIcons from '@unocss/preset-icons/browser'

presetIcons({
  collections: {
    carbon: () => import('@iconify-json/carbon/icons.json').then(i => i.default),
  }
})
```

## 图标自定义

```ts
presetIcons({
  customizations: {
    // 转换 SVG
    transform(svg, collection, icon) {
      return svg.replace(/#fff/, 'currentColor')
    },
    // 全局大小
    customize(props) {
      props.width = '2em'
      props.height = '2em'
      return props
    },
    // 每个集合
    iconCustomizer(collection, icon, props) {
      if (collection === 'mdi') {
        props.width = '2em'
      }
    }
  }
})
```

## CSS 指令

在 CSS 中使用 `icon()`（需要 transformer-directives）：

```css
.icon {
  background-image: icon('i-carbon-sun');
}
.icon-colored {
  background-image: icon('i-carbon-moon', '#fff');
}
```

## 可访问性

```html
<!-- 带标签 -->
<a href="/profile" aria-label="Profile" class="i-ph:user-duotone"></a>

<!-- 装饰性 -->
<a href="/profile">
  <span aria-hidden="true" class="i-ph:user-duotone"></span>
  My Profile
</a>
```

<!--
源参考：
- https://unocss.dev/presets/icons
-->
