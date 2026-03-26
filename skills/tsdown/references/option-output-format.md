# 输出格式

配置生成包的模块格式。

## 概述

tsdown 可以生成多种格式的包。默认为 ESM。

## 可用格式

| 格式 | 描述 | 用例 |
|--------|-------------|----------|
| `esm` | ECMAScript 模块（默认） | 现代 Node.js、浏览器、Deno |
| `cjs` | CommonJS | 传统 Node.js、require() |
| `iife` | 立即调用函数表达式 | 浏览器 `<script>` 标签 |
| `umd` | 通用模块定义 | AMD、CommonJS 和全局变量 |

## 使用

### CLI

```bash
# 单个格式
tsdown --format esm

# 多个格式
tsdown --format esm --format cjs

# 或逗号分隔
tsdown --format esm,cjs
```

### 配置文件

#### 单个格式

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: 'esm',
})
```

#### 多个格式

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
})
```

## 每种格式配置

为特定格式覆盖选项：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: {
    esm: {
      target: ['es2015'],
    },
    cjs: {
      target: ['node20'],
    },
  },
})
```

这允许每种格式有不同的目标、平台或其他设置。

## 常见模式

### 现代库（ESM + CJS）

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
})
```

输出：
- `dist/index.mjs` (ESM)
- `dist/index.cjs` (CJS)
- `dist/index.d.ts` (类型)

### 浏览器库（IIFE）

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['iife'],
  globalName: 'MyLib',
  platform: 'browser',
  minify: true,
})
```

输出：`dist/index.global.js`（带有全局变量 `MyLib` 的 IIFE）

### 通用库（UMD）

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['umd'],
  globalName: 'MyLib',
  platform: 'neutral',
})
```

适用于 AMD、CommonJS 和浏览器全局变量。

### Node.js 包（CJS + ESM）

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  platform: 'node',
  dts: true,
  shims: true, // 为 CJS 兼容性添加 __dirname、__filename
})
```

### 框架组件库

```ts
export default defineConfig({
  entry: ['src/index.tsx'],
  format: ['esm', 'cjs'],
  deps: {
    neverBundle: ['react', 'react-dom'], // 不打包依赖项
  },
  dts: true,
})
```

## 特定格式的输出

### 文件扩展名

| 格式 | 扩展名 |
|--------|-----------|
| ESM | `.mjs` 或 `.js`（带有 `"type": "module"`） |
| CJS | `.cjs` 或 `.js`（不带有 `"type": "module"`） |
| IIFE | `.global.js` |
| UMD | `.umd.js` |

### 自定义扩展名

使用 `outExtensions` 覆盖：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  outExtensions: ({ format }) => ({
    js: format === 'esm' ? '.js' : '.cjs',
  }),
})
```

## 提示

1. **使用 ESM + CJS**以获得最大兼容性
2. **使用 IIFE**用于仅浏览器的库
3. **使用 UMD**用于通用兼容性（现在较少见）
4. **外部化依赖项**以避免打包框架代码
5. **添加 shims**以在使用 Node.js API 时提供 CJS 兼容性
6. **设置 globalName**用于 IIFE/UMD 格式

## 相关选项

- [目标](option-target.md) - 设置 JavaScript 版本
- [平台](option-platform.md) - 设置平台（node、browser、neutral）
- [Shims](option-shims.md) - 添加 ESM/CJS 兼容性
- [输出目录](option-output-directory.md) - 自定义输出路径
