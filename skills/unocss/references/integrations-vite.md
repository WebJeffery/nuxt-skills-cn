---
name: unocss-vite-integration
description: 使用 Vite 设置 UnoCSS 和特定框架的提示
---

# UnoCSS Vite 集成

Vite 插件是使用 UnoCSS 的最常见方式。

## 安装

```bash
pnpm add -D unocss
```

```ts
// vite.config.ts
import UnoCSS from 'unocss/vite'
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [
    UnoCSS(),
  ],
})
```

创建配置文件：

```ts
// uno.config.ts
import { defineConfig, presetWind3 } from 'unocss'

export default defineConfig({
  presets: [
    presetWind3(),
  ],
})
```

添加到入口：

```ts
// main.ts
import 'virtual:uno.css'
```

## 模式

### global（默认）

标准模式 - 通过 `uno.css` 导入生成全局 CSS。

```ts
import 'virtual:uno.css'
```

### vue-scoped

将生成的 CSS 注入到 Vue SFC `<style scoped>`。

```ts
UnoCSS({
  mode: 'vue-scoped',
})
```

### shadow-dom

对于使用 Shadow DOM 的 Web 组件：

```ts
UnoCSS({
  mode: 'shadow-dom',
})
```

在组件样式中添加占位符：

```ts
const template = document.createElement('template')
template.innerHTML = `
<style>
  :host { ... }
  @unocss-placeholder
</style>
<div class="m-1em">...</div>
`
```

### per-module（实验性）

为每个模块生成 CSS，带有可选的作用域。

### dist-chunk（实验性）

在构建时为每个块生成 CSS，用于 MPA。

## DevTools 支持

直接在浏览器 DevTools 中编辑类：

```ts
import 'virtual:uno.css'
import 'virtual:unocss-devtools'
```

**警告：** 使用 MutationObserver 检测更改。来自脚本的动态类也将被包含。

## 特定框架设置

### React

```ts
// vite.config.ts
import React from '@vitejs/plugin-react'
import UnoCSS from 'unocss/vite'

export default {
  plugins: [
    UnoCSS(), // 使用 attributify 时必须在 React 之前
    React(),
  ],
}
```

**注意：** 如果使用 `@unocss/preset-attributify`，请从构建脚本中删除 `tsc`。

### Vue

与 `@vitejs/plugin-vue` 开箱即用。

### Svelte

```ts
import { svelte } from '@sveltejs/vite-plugin-svelte'
import extractorSvelte from '@unocss/extractor-svelte'
import UnoCSS from 'unocss/vite'

export default {
  plugins: [
    UnoCSS({
      extractors: [extractorSvelte()],
    }),
    svelte(),
  ],
}
```

支持 `class:foo` 和 `class:foo={bar}` 语法。

### SvelteKit

与 Svelte 相同，使用 `@sveltejs/kit/vite` 中的 `sveltekit()`。

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

### Preact

```ts
import Preact from '@preact/preset-vite'
import UnoCSS from 'unocss/vite'

export default {
  plugins: [
    UnoCSS(),
    Preact(),
  ],
}
```

### Elm

```ts
import Elm from 'vite-plugin-elm'
import UnoCSS from 'unocss/vite'

export default {
  plugins: [
    Elm(),
    UnoCSS(),
  ],
}
```

### Web Components (Lit)

```ts
UnoCSS({
  mode: 'shadow-dom',
  shortcuts: [
    { 'cool-blue': 'bg-blue-500 text-white' },
  ],
})
```

```ts
// my-element.ts
@customElement('my-element')
export class MyElement extends LitElement {
  static styles = css`
    :host { ... }
    @unocss-placeholder
  `
}
```

支持 `part-[<part-name>]:<utility>` 用于 `::part` 样式。

## 检查器

在开发模式下访问 `http://localhost:5173/__unocss` 以：

- 检查生成的 CSS 规则
- 查看每个文件应用的类
- 在 REPL 中测试工具类

## 旧版浏览器支持

使用 `@vitejs/plugin-legacy`：

```ts
import legacy from '@vitejs/plugin-legacy'
import UnoCSS from 'unocss/vite'

export default {
  plugins: [
    UnoCSS({
      legacy: {
        renderModernChunks: false,
      },
    }),
    legacy({
      targets: ['defaults', 'not IE 11'],
      renderModernChunks: false,
    }),
  ],
}
```

## VanillaJS / TypeScript

默认情况下，`.js` 和 `.ts` 文件不被提取。配置以包含：

```ts
// uno.config.ts
export default defineConfig({
  content: {
    pipeline: {
      include: [
        /\.(vue|svelte|[jt]sx|html)($|\?)/,
        'src/**/*.{js,ts}',
      ],
    },
  },
})
```

或在文件中使用魔法注释：

```ts
// @unocss-include
export const classes = {
  active: 'bg-primary text-white',
}
```

<!--
源参考：
- https://unocss.dev/integrations/vite
-->
