# 压缩

压缩代码以减小包大小。

## 概述

压缩删除不必要的字符（空白、注释）并优化代码以用于生产，减小包大小并提高加载时间。

**注意：** 内部使用 [Oxc 压缩器](https://oxc.rs/docs/contribute/minifier)。压缩器目前处于 alpha 阶段。

## 类型

```ts
minify?: boolean | 'dce-only' | MinifyOptions
```

- `true` — 启用完整压缩（空白删除、混淆、压缩）
- `false` — 禁用压缩（默认）
- `'dce-only'` — 仅执行死代码消除而不进行完整压缩
- `MinifyOptions` — 向 Oxc 压缩器传递详细选项

## 基本用法

### CLI

```bash
# 启用压缩
tsdown --minify

# 禁用压缩
tsdown --no-minify
```

**注意：** CLI `--minify` 标志是布尔切换。对于 `'dce-only'` 模式或高级选项，请使用配置文件。

### 配置文件

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  minify: true,
})
```

### DCE-Only 模式

删除死代码而不进行完整压缩（保持输出可读）：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  minify: 'dce-only',
})
```

## 示例输出

### 不压缩

```js
// dist/index.mjs
const x = 1

function hello(x$1) {
  console.log('Hello World')
  console.log(x$1)
}

hello(x)
```

### 压缩

```js
// dist/index.mjs
const e=1;function t(e){console.log(`Hello World`),console.log(e)}t(e);
```

## 常见模式

### 生产构建

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  minify: true,
  clean: true,
})
```

### 条件压缩

```ts
export default defineConfig((options) => ({
  entry: ['src/index.ts'],
  format: ['esm'],
  minify: !options.watch,  // 仅在生产中压缩
}))
```

### 浏览器库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['iife'],
  platform: 'browser',
  globalName: 'MyLib',
  minify: true,
})
```

### 多次构建

```ts
export default defineConfig([
  // 开发构建
  {
    entry: ['src/index.ts'],
    format: ['esm'],
    minify: false,
    outDir: 'dist/dev',
  },
  // 生产构建
  {
    entry: ['src/index.ts'],
    format: ['esm'],
    minify: true,
    outDir: 'dist/prod',
  },
])
```

## CLI 示例

```bash
# 带压缩的生产构建
tsdown --minify --clean

# 多种格式并压缩
tsdown --format esm --format cjs --minify

# 条件压缩（仅在不监视时）
tsdown --minify  # 或省略 --watch
```

## 提示

1. **使用 `minify: true`**用于生产构建
2. **使用 `'dce-only'`**删除死代码同时保持输出可读
3. **在开发期间跳过压缩**以加快重新构建
4. **与 tree shaking 结合**以获得最佳结果
5. **彻底测试压缩输出**（Oxc 压缩器处于 alpha 阶段）

## 故障排除

### 压缩代码有错误

Oxc 压缩器处于 alpha 阶段，可能存在问题：

1. **使用 DCE-only 模式**：`minify: 'dce-only'`
2. **报告错误**到 [Oxc 项目](https://github.com/oxc-project/oxc/issues)
3. **禁用压缩**：`minify: false`

### 意外的输出

- **首先测试未压缩**以隔离问题
- **检查源映射**用于调试
- **验证目标兼容性**

## 相关选项

- [Tree Shaking](option-tree-shaking.md) - 删除未使用的代码
- [目标](option-target.md) - 语法转换
- [输出格式](option-output-format.md) - 模块格式
- [源映射](option-sourcemap.md) - 调试信息
