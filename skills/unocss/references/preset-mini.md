---
name: preset-mini
description: UnoCSS 的最小预设，包含基本工具类
---

# 预设 Mini

仅包含基本规则和变体的最小预设。自定义预设的良好起点。

## 安装

```ts
import { defineConfig, presetMini } from 'unocss'

export default defineConfig({
  presets: [
    presetMini(),
  ],
})
```

## 包含内容

`preset-wind3` 的子集，包含与 CSS 属性对齐的基本工具类：

- 基本间距（margin、padding）
- 显示（flex、grid、block 等）
- 定位（absolute、relative、fixed）
- 大小（width、height）
- 颜色（text、background、border）
- 基本排版（font-size、font-weight）
- 边框和边框半径
- 基本变换和过渡

## 不包含内容

固执或复杂的 Tailwind 工具类：
- `container`
- 复杂动画
- 渐变
- 高级排版
- Prose 类

## 使用场景

1. **构建自定义预设** - 从 mini 开始，只添加您需要的
2. **最小包大小** - 当您只需要基本工具类时
3. **学习** - 在没有 Tailwind 复杂性的情况下了解 UnoCSS 核心

## 暗色模式

与 preset-wind3 相同：

```ts
presetMini({
  dark: 'class' // 或 'media'
})
```

```html
<div class="dark:bg-red:10" />
```

基于类：
```css
.dark .dark\:bg-red\:10 {
  background-color: rgb(248 113 113 / 0.1);
}
```

媒体查询：
```css
@media (prefers-color-scheme: dark) {
  .dark\:bg-red\:10 {
    background-color: rgb(248 113 113 / 0.1);
  }
}
```

## CSS @layer 变体

原生 CSS 图层支持：

```html
<div class="layer-foo:p4" />
```

```css
@layer foo {
  .layer-foo\:p4 {
    padding: 1rem;
  }
}
```

## 主题自定义

```ts
presetMini({
  theme: {
    colors: {
      veryCool: '#0000ff',
      brand: {
        primary: 'hsl(var(--hue, 217) 78% 51%)',
      }
    },
  }
})
```

**注意：** `breakpoints` 属性被覆盖，而不是合并。

## 选项

```ts
presetMini({
  // 暗色模式：'class' | 'media' | { light: string, dark: string }
  dark: 'class',
  
  // 为 attributify 生成 [group=""] 而不是 .group
  attributifyPseudo: false,
  
  // CSS 变量前缀（默认：'un-'）
  variablePrefix: 'un-',
  
  // 工具类前缀
  prefix: undefined,
  
  // 预置生成：true | false | 'on-demand'
  preflight: true,
})
```

## 在 Mini 基础上构建

创建扩展 mini 的自定义预设：

```ts
import { presetMini } from 'unocss'
import type { Preset } from 'unocss'

export const myPreset: Preset = {
  name: 'my-preset',
  presets: [presetMini()],
  rules: [
    // 添加自定义规则
    ['card', { 'border-radius': '8px', 'box-shadow': '0 2px 8px rgba(0,0,0,0.1)' }],
  ],
  shortcuts: {
    'btn': 'px-4 py-2 rounded bg-blue-500 text-white',
  },
}
```

<!--
源参考：
- https://unocss.dev/presets/mini
-->
