# 配置文件

使用配置文件集中和管理构建设置。

## 概述

tsdown 会自动在当前目录和父目录中搜索配置文件。

## 支持的文件名

tsdown 按顺序查找以下文件：
- `tsdown.config.ts`
- `tsdown.config.mts`
- `tsdown.config.cts`
- `tsdown.config.js`
- `tsdown.config.mjs`
- `tsdown.config.cjs`
- `tsdown.config.json`
- `tsdown.config`
- `package.json`（在 `tsdown` 字段中）

## 基本配置

### TypeScript 配置

```ts
// tsdown.config.ts
import { defineConfig } from 'tsdown'

export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  clean: true,
})
```

### JavaScript 配置

```js
// tsdown.config.js
export default {
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
}
```

### JSON 配置

```json
// tsdown.config.json
{
  "entry": ["src/index.ts"],
  "format": ["esm", "cjs"],
  "dts": true
}
```

### Package.json 配置

```json
// package.json
{
  "name": "my-library",
  "tsdown": {
    "entry": ["src/index.ts"],
    "format": ["esm", "cjs"],
    "dts": true
  }
}
```

## 多个配置

使用不同设置构建多个输出：

```ts
export default defineConfig([
  {
    entry: 'src/index.ts',
    format: ['esm', 'cjs'],
    platform: 'node',
    dts: true,
  },
  {
    entry: 'src/browser.ts',
    format: ['iife'],
    platform: 'browser',
    globalName: 'MyLib',
    minify: true,
  },
])
```

每个配置作为单独的构建运行。

## 动态配置

使用函数进行条件配置：

```ts
export default defineConfig((options) => {
  const isDev = options.watch

  return {
    entry: ['src/index.ts'],
    format: ['esm', 'cjs'],
    minify: !isDev,
    sourcemap: isDev,
    clean: !isDev,
  }
})
```

可用选项：
- `watch` - 是否启用监视模式
- 传递给配置的其他 CLI 标志

## 配置加载器

控制如何加载 TypeScript 配置文件：

### 自动加载器（默认）

如果可用，使用原生 TypeScript 支持，否则回退到 `unrun`：

```bash
tsdown # 使用自动加载器
```

### 原生加载器

使用运行时的原生 TypeScript 支持（Node.js 23+、Bun、Deno）：

```bash
tsdown --config-loader native
```

### Unrun 加载器

使用 [unrun](https://gugustinette.github.io/unrun/) 库进行加载：

```bash
tsdown --config-loader unrun
```

**提示：** 如果需要在 Node.js 中加载没有文件扩展名的 TypeScript 配置，请使用 `unrun` 加载器。

## 自定义配置路径

指定自定义配置文件位置：

```bash
tsdown --config ./configs/build.config.ts
# 或
tsdown -c custom-config.ts
```

## 禁用配置文件

忽略配置文件，仅使用 CLI 选项：

```bash
tsdown --no-config src/index.ts --format esm
```

## 扩展 Vite/Vitest 配置（实验性）

重用现有的 Vite 或 Vitest 配置：

```bash
# 扩展 vite.config.*
tsdown --from-vite

# 扩展 vitest.config.*
tsdown --from-vite vitest
```

**注意：** 仅重用特定选项，如 `resolve` 和 `plugins`。由于此功能是实验性的，请充分测试。

## 工作区 / Monorepo

使用单个配置构建多个包：

```ts
export default defineConfig({
  workspace: 'packages/*',
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
})
```

每个匹配 glob 模式的包目录都将使用相同的配置进行构建。

## 常见模式

### 带有多个构建的库

```ts
export default defineConfig([
  // Node.js 构建
  {
    entry: ['src/index.ts'],
    format: ['esm', 'cjs'],
    platform: 'node',
    dts: true,
  },
  // 浏览器构建
  {
    entry: ['src/browser.ts'],
    format: ['iife'],
    platform: 'browser',
    globalName: 'MyLib',
  },
])
```

### 开发 vs 生产

```ts
export default defineConfig((options) => ({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  minify: !options.watch,
  sourcemap: options.watch ? true : false,
  clean: !options.watch,
}))
```

### Monorepo 根配置

```ts
// Root tsdown.config.ts
export default defineConfig({
  workspace: 'packages/*',
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  clean: true,
  // 所有包的共享配置
})
```

### 每个包的覆盖

```ts
// packages/special/tsdown.config.ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'], // 覆盖：仅 ESM
  platform: 'browser', // 覆盖：仅浏览器
})
```

## 配置优先级

当存在多个配置时：

1. CLI 选项（最高优先级）
2. 使用 `--config` 指定的配置文件
3. 自动发现的配置文件
4. Package.json `tsdown` 字段
5. 默认值

## 提示

1. **使用 TypeScript 配置**以进行类型检查和自动完成
2. **使用 defineConfig** 辅助函数以获得更好的 DX
3. **导出数组**以进行多个构建配置
4. **使用函数**进行动态/条件配置
5. **保持配置简单** - 优先使用约定而非配置
6. **使用 workspace**进行 monorepo 构建
7. 在生产使用前充分测试实验性功能

## 相关选项

- [入口点](option-entry.md) - 配置入口点
- [输出格式](option-output-format.md) - 输出格式
- [监视模式](option-watch-mode.md) - 监视模式配置
