---
name: antfu-eslint-config
description: 配置 @antfu/eslint-config 以支持框架、格式化工具和规则覆盖。用于添加 React/Vue/Svelte/Astro 支持、自定义规则或设置 VS Code 集成。
---

# @antfu/eslint-config

同时处理 linting 和格式化 (无需 Prettier)。自动检测 TypeScript 和 Vue。

**风格**: 单引号、无分号、排序的导入、尾随逗号。

## 配置选项

```js
import antfu from '@antfu/eslint-config'

export default antfu({
  // 项目类型: 'lib' 用于库，'app' (默认) 用于应用
  type: 'lib',

  // 全局忽略 (扩展默认值，不覆盖)
  ignores: ['**/fixtures', '**/dist'],

  // 样式选项
  stylistic: {
    indent: 2,        // 2, 4, 或 'tab'
    quotes: 'single', // 或 'double'
  },

  // 框架支持 (自动检测，但可以显式指定)
  typescript: true,
  vue: true,

  // 禁用特定语言支持
  jsonc: false,
  yaml: false,
})
```

## 框架支持

### Vue

Vue 可访问性:

```js
export default antfu({
  vue: {
    a11y: true
  },
})
// 需要: pnpm add -D eslint-plugin-vuejs-accessibility
```

### React

```js
export default antfu({
  react: true,
})
// 需要: pnpm add -D @eslint-react/eslint-plugin eslint-plugin-react-hooks eslint-plugin-react-refresh
```

### Next.js

```js
export default antfu({
  nextjs: true,
})
// 需要: pnpm add -D @next/eslint-plugin-next
```

### Svelte

```js
export default antfu({
  svelte: true,
})
// 需要: pnpm add -D eslint-plugin-svelte
```

### Astro

```js
export default antfu({
  astro: true,
})
// 需要: pnpm add -D eslint-plugin-astro
```

### Solid

```js
export default antfu({
  solid: true,
})
// 需要: pnpm add -D eslint-plugin-solid
```

### UnoCSS

```js
export default antfu({
  unocss: true,
})
// 需要: pnpm add -D @unocss/eslint-plugin
```

## 格式化工具 (CSS, HTML, Markdown)

对于 ESLint 原生不支持的文件:

```js
export default antfu({
  formatters: {
    css: true,      // 格式化 CSS, LESS, SCSS (使用 Prettier)
    html: true,     // 格式化 HTML (使用 Prettier)
    markdown: 'prettier' // 或 'dprint'
  }
})
// 需要: pnpm add -D eslint-plugin-format
```

## 规则覆盖

### 全局覆盖

```js
export default antfu(
  {
    // 第一个参数: antfu 配置选项
  },
  // 额外参数: ESLint flat configs
  {
    rules: {
      'style/semi': ['error', 'never'],
    },
  }
)
```

### 每个集成的覆盖

```js
export default antfu({
  vue: {
    overrides: {
      'vue/operator-linebreak': ['error', 'before'],
    },
  },
  typescript: {
    overrides: {
      'ts/consistent-type-definitions': ['error', 'interface'],
    },
  },
})
```

### 文件特定覆盖

```js
export default antfu(
  { vue: true, typescript: true },
  {
    files: ['**/*.vue'],
    rules: {
      'vue/operator-linebreak': ['error', 'before'],
    },
  }
)
```

## 插件前缀重命名

配置重命名插件前缀以保持一致性:

| 新前缀 | 原始 |
|------------|----------|
| `ts/*` | `@typescript-eslint/*` |
| `style/*` | `@stylistic/*` |
| `import/*` | `import-lite/*` |
| `node/*` | `n/*` |
| `yaml/*` | `yml/*` |
| `test/*` | `vitest/*` |
| `next/*` | `@next/next` |

在覆盖或禁用规则时使用新前缀:

```ts
// eslint-disable-next-line ts/consistent-type-definitions
type Foo = { bar: 2 }
```

## 类型感知规则

启用 TypeScript 类型检查:

```js
export default antfu({
  typescript: {
    tsconfigPath: 'tsconfig.json',
  },
})
```

## 配置组合器 API

链式调用方法以灵活组合:

```js
export default antfu()
  .prepend(/* 主配置之前的配置 */)
  .override('antfu/stylistic/rules', {
    rules: {
      'style/generator-star-spacing': ['error', { after: true, before: false }],
    }
  })
  .renamePlugins({
    'old-prefix': 'new-prefix',
  })
```

## 更少固执己见的模式

禁用 白川 最有争议的规则:

```js
export default antfu({
  lessOpinionated: true
})
```

## Lint-Staged 设置

```json
{
  "simple-git-hooks": {
    "pre-commit": "pnpm lint-staged"
  },
  "lint-staged": {
    "*": "eslint --fix"
  }
}
```

```bash
pnpm add -D lint-staged simple-git-hooks
npx simple-git-hooks
```

## VS Code 设置

添加到 `.vscode/settings.json`:

```jsonc
{
  "prettier.enable": false,
  "editor.formatOnSave": false,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "never"
  },
  "eslint.rules.customizations": [
    { "rule": "style/*", "severity": "off", "fixable": true },
    { "rule": "format/*", "severity": "off", "fixable": true },
    { "rule": "*-indent", "severity": "off", "fixable": true },
    { "rule": "*-spacing", "severity": "off", "fixable": true },
    { "rule": "*-spaces", "severity": "off", "fixable": true },
    { "rule": "*-order", "severity": "off", "fixable": true },
    { "rule": "*-dangle", "severity": "off", "fixable": true },
    { "rule": "*-newline", "severity": "off", "fixable": true },
    { "rule": "*quotes", "severity": "off", "fixable": true },
    { "rule": "*semi", "severity": "off", "fixable": true }
  ],
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "vue",
    "html",
    "markdown",
    "json",
    "jsonc",
    "yaml",
    "toml",
    "xml",
    "astro",
    "svelte",
    "css",
    "less",
    "scss"
  ]
}
```

<!--
源引用:
- https://github.com/antfu/eslint-config
- https://raw.githubusercontent.com/antfu/eslint-config/refs/heads/main/README.md
-->
