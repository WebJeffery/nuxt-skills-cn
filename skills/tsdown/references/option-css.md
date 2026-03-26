# CSS 支持

**状态：实验性 - API 和行为可能会更改。**

配置 CSS 处理，包括预处理器、语法降级、压缩和代码分割。

## 入门指南

`tsdown` 中的所有 CSS 支持都由 `@tsdown/css` 包提供。安装它以启用 CSS 处理：

```bash
npm install -D @tsdown/css
```

安装 `@tsdown/css` 后，CSS 处理会自动启用。没有它，遇到 CSS 文件将导致错误。

## CSS 导入

从 TypeScript/JavaScript 导入 `.css` 文件 — CSS 被提取到单独的 `.css` 资源中：

```ts
// src/index.ts
import './style.css'
export function greet() { return 'Hello' }
```

输出：`index.mjs` + `index.css`

### `@import` 内联

CSS `@import` 语句会自动解析和内联。不生成单独的输出文件。

### 内联 CSS (`?inline`)

附加 `?inline` 以将处理的 CSS 作为 JS 字符串返回，而不是发出 `.css` 文件：

```ts
import './style.css'                   // → .css 文件
import css from './theme.css?inline'   // → JS 字符串
```

也适用于预处理器（`./foo.scss?inline`）。经过完整管道（预处理器、@import 内联、降级、压缩）。可 tree-shake（`moduleSideEffects: false`）。

## CSS 预处理器

内置支持 Sass、Less 和 Stylus。安装预处理器：

```bash
# Sass（任一）
npm install -D sass-embedded  # 推荐，更快
npm install -D sass

# Less
npm install -D less

# Stylus
npm install -D stylus
```

然后直接导入：

```ts
import './style.scss'
import './theme.less'
import './global.styl'
```

### 预处理器选项

```ts
export default defineConfig({
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `$brand-color: #ff7e17;`,
      },
      less: {
        math: 'always',
      },
      stylus: {
        define: { '$brand-color': '#ff7e17' },
      },
    },
  },
})
```

### `additionalData`

在每个预处理器文件的开头注入代码：

```ts
// 字符串形式
scss: {
  additionalData: `@use "src/styles/variables" as *;`,
}

// 函数形式
scss: {
  additionalData: (source, filename) => {
    if (filename.includes('theme')) return source
    return `@use "src/styles/variables" as *;\n${source}`
  },
}
```

## CSS 压缩

```ts
export default defineConfig({
  css: {
    minify: true,
  },
})
```

由 Lightning CSS 提供支持。

## CSS 目标

专门为 CSS 覆盖顶级 `target`：

```ts
export default defineConfig({
  target: 'node18',
  css: {
    target: 'chrome90', // CSS 特定目标
  },
})
```

设置 `css.target: false` 以完全禁用 CSS 语法降级。

## CSS 转换器

`css.transformer` 控制互斥的 CSS 处理路径：

- `'lightningcss'`（默认）：通过 Lightning CSS `bundleAsync()` 进行 `@import`，无 PostCSS。
- `'postcss'`：通过 `postcss-import` 进行 `@import`，应用 PostCSS 插件，仅对最终转换使用 Lightning CSS。

```ts
export default defineConfig({
  css: {
    transformer: 'postcss',
  },
})
```

### PostCSS 选项

```ts
export default defineConfig({
  css: {
    transformer: 'postcss',
    postcss: {
      plugins: [require('autoprefixer')],
    },
    // 或：postcss: './config' — 搜索 postcss.config.js 的路径
  },
})
```

当 `transformer` 为 `'postcss'` 且省略 `css.postcss` 时，从项目根目录自动检测 PostCSS 配置。

## Lightning CSS（语法降级）

安装 `lightningcss` 以根据您的 `target` 启用 CSS 语法降级：

```bash
npm install -D lightningcss
```

设置 `target` 时（例如，`target: 'chrome108'`），现代 CSS 特性会自动降级：

```css
/* 输入 */
.foo { & .bar { color: red } }

/* 输出 (chrome108) */
.foo .bar { color: red }
```

### 自定义 Lightning CSS 选项

```ts
import { Features } from 'lightningcss'

export default defineConfig({
  css: {
    lightningcss: {
      targets: { chrome: 100 << 16 },
      include: Features.Nesting,
    },
  },
})
```

对于 CSS，`css.lightningcss.targets` 优先于 `target` 和 `css.target`。

## 代码分割

### 合并（默认）

所有 CSS 合并为单个文件（默认：`style.css`）。

```ts
export default defineConfig({
  css: {
    fileName: 'my-library.css', // 自定义名称（默认：'style.css'）
  },
})
```

### 每个块分割

```ts
export default defineConfig({
  css: {
    splitting: true, // 每个 JS 块都有对应的 .css 文件
  },
})
```

## 保留 CSS 导入（`css.inject`）

启用时，JS 输出保留指向发出的 CSS 文件的 `import` 语句。使用者会自动与 JS 一起导入 CSS：

```ts
export default defineConfig({
  css: {
    inject: true,
  },
})
```

## 选项参考

| 选项 | 类型 | 默认值 | 描述 |
|--------|------|---------|-------------|
| `css.transformer` | `'postcss' \| 'lightningcss'` | `'lightningcss'` | CSS 处理管道 |
| `css.splitting` | `boolean` | `false` | 每个块的 CSS 分割 |
| `css.fileName` | `string` | `'style.css'` | 合并的 CSS 文件名 |
| `css.minify` | `boolean` | `false` | CSS 压缩 |
| `css.inject` | `boolean` | `false` | 在 JS 输出中保留 CSS 导入 |
| `css.target` | `string \| string[] \| false` | _来自 `target`_ | CSS 特定降级目标 |
| `css.postcss` | `string \| object` | — | PostCSS 配置路径或内联选项 |
| `css.preprocessorOptions` | `object` | — | 预处理器选项 |
| `css.lightningcss` | `object` | — | Lightning CSS 选项 |

## 相关

- [目标](option-target.md) - 配置语法降级目标
- [输出格式](option-output-format.md) - 模块输出格式