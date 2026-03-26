---
name: transformer-attributify-jsx
description: 在 JSX/TSX 中支持无值 attributify
---

# Transformer Attributify JSX

修复 JSX 中的无值 attributify 模式，其中 `<div foo>` 变为 `<div foo={true}>`。

## 问题

在 JSX 中，无值属性被转换：

```jsx
// 您编写
<div m-2 rounded text-teal-400 />

// JSX 编译为
<div m-2={true} rounded={true} text-teal-400={true} />
```

`={true}` 破坏了 UnoCSS attributify 检测。

## 安装

```ts
import { 
  defineConfig, 
  presetAttributify, 
  transformerAttributifyJsx 
} from 'unocss'

export default defineConfig({
  presets: [
    presetAttributify(),
  ],
  transformers: [
    transformerAttributifyJsx(),
  ],
})
```

## 工作原理

转换器将 JSX 布尔属性转换回字符串：

```jsx
// 输入（JSX 编译后）
<div m-2={true} rounded={true} />

// 输出（转换后）
<div m-2="" rounded="" />
```

现在 UnoCSS 可以正确提取 attributify 类。

## 选项

```ts
transformerAttributifyJsx({
  // 仅转换特定属性
  // 默认：转换所有匹配 attributify 模式的属性
  blocklist: ['text', 'font'],
})
```

## 何时使用

使用以下内容时必需：
- React
- Preact
- Solid
- 任何基于 JSX 的框架

使用无值 attributify 语法：

```jsx
// 这需要转换器
<div flex items-center gap-4 />

// 这在没有转换器的情况下工作（有值）
<div flex="~" items="center" gap="4" />
```

## 框架设置

### React

```ts
// vite.config.ts
import React from '@vitejs/plugin-react'
import UnoCSS from 'unocss/vite'

export default {
  plugins: [
    UnoCSS(), // 必须在 React 之前
    React(),
  ],
}
```

```ts
// uno.config.ts
import { 
  defineConfig, 
  presetAttributify, 
  presetWind3,
  transformerAttributifyJsx 
} from 'unocss'

export default defineConfig({
  presets: [
    presetWind3(),
    presetAttributify(),
  ],
  transformers: [
    transformerAttributifyJsx(),
  ],
})
```

### Preact

与 React 相同，使用 `@preact/preset-vite` 或 `@prefresh/vite`。

### Solid

```ts
import UnoCSS from 'unocss/vite'
import solidPlugin from 'vite-plugin-solid'

export default {
  plugins: [
    UnoCSS(),
    solidPlugin(),
  ],
}
```

## TypeScript 支持

添加类型声明：

```ts
// shims.d.ts
import type { AttributifyAttributes } from '@unocss/preset-attributify'

declare module 'react' {
  interface HTMLAttributes<T> extends AttributifyAttributes {}
}
```

<!--
源参考：
- https://unocss.dev/transformers/attributify-jsx
-->
