---
name: preset-typography
description: 用于原生 HTML 内容的排版默认值的 Prose 类
---

# 预设 Typography

用于为原生 HTML 内容添加排版默认值的 Prose 类。

## 安装

```ts
import { defineConfig, presetTypography, presetWind3 } from 'unocss'

export default defineConfig({
  presets: [
    presetWind3(), // 必需！
    presetTypography(),
  ],
})
```

## 基本用法

```html
<article class="prose">
  <h1>My Article</h1>
  <p>This is styled with typographic defaults...</p>
</article>
```

## 大小

```html
<article class="prose prose-sm">Small</article>
<article class="prose prose-base">Base (default)</article>
<article class="prose prose-lg">Large</article>
<article class="prose prose-xl">Extra large</article>
<article class="prose prose-2xl">2X large</article>
```

响应式：
```html
<article class="prose prose-sm md:prose-base lg:prose-lg">
  Responsive typography
</article>
```

## 颜色

```html
<article class="prose prose-gray">Gray (default)</article>
<article class="prose prose-slate">Slate</article>
<article class="prose prose-blue">Blue</article>
```

## 暗色模式

```html
<article class="prose dark:prose-invert">
  Dark mode typography
</article>
```

## 排除元素

```html
<article class="prose">
  <p>Styled</p>
  <div class="not-prose">
    <p>NOT styled</p>
  </div>
</article>
```

**注意：** `not-prose` 只能作为类使用。

## 选项

```ts
presetTypography({
  selectorName: 'prose',      // 自定义选择器
  cssVarPrefix: '--un-prose', // CSS 变量前缀
  important: false,           // 使 !important
  cssExtend: {
    'code': { color: '#8b5cf6' },
    'a:hover': { color: '#f43f5e' },
  },
})
```

<!--
源参考：
- https://unocss.dev/presets/typography
-->
