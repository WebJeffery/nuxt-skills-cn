---
name: tsdown
description: 使用 Rolldown 驱动的超快速度打包 TypeScript 和 JavaScript 库。用于构建库、生成类型声明、打包多种格式或从 tsup 迁移。
---

# tsdown - 优雅的库打包器

由 Rolldown 和 Oxc 驱动的超快 TypeScript/JavaScript 库打包器。

## 使用场景

- 为 npm 构建 TypeScript/JavaScript 库
- 生成 TypeScript 声明文件（.d.ts）
- 打包多种格式（ESM、CJS、IIFE、UMD）
- 使用 tree shaking 和压缩优化包
- 从 tsup 迁移，只需最少更改
- 构建 React、Vue、Solid 或 Svelte 组件库

## 快速开始

```bash
# 安装
pnpm add -D tsdown

# 基本用法
npx tsdown

# 使用配置文件
npx tsdown --config tsdown.config.ts

# 监视模式
npx tsdown --watch

# 从 tsup 迁移
npx tsdown-migrate
```

## 基本配置

```ts
import { defineConfig } from 'tsdown'

export default defineConfig({
  entry: ['./src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  clean: true,
})
```

## 核心参考

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 入门 | 安装、首次打包、CLI 基础 | [guide-getting-started](references/guide-getting-started.md) |
| 配置文件 | 配置文件格式、多个配置、工作区 | [option-config-file](references/option-config-file.md) |
| CLI 参考 | 所有 CLI 命令和选项 | [reference-cli](references/reference-cli.md) |
| 从 tsup 迁移 | 迁移指南和兼容性说明 | [guide-migrate-from-tsup](references/guide-migrate-from-tsup.md) |
| 插件 | Rolldown、Rollup、Unplugin 支持 | [advanced-plugins](references/advanced-plugins.md) |
| 钩子 | 自定义逻辑的生命周期钩子 | [advanced-hooks](references/advanced-hooks.md) |
| 编程式 API | 从 Node.js 脚本构建 | [advanced-programmatic](references/advanced-programmatic.md) |
| Rolldown 选项 | 直接将选项传递给 Rolldown | [advanced-rolldown-options](references/advanced-rolldown-options.md) |
| CI 环境 | CI 检测、'ci-only' / 'local-only' 值 | [advanced-ci](references/advanced-ci.md) |

## 构建选项

| 选项 | 用法 | 参考 |
|--------|-------|-----------|
| 入口点 | `entry: ['src/*.ts', '!**/*.test.ts']` | [option-entry](references/option-entry.md) |
| 输出格式 | `format: ['esm', 'cjs', 'iife', 'umd']` | [option-output-format](references/option-output-format.md) |
| 输出目录 | `outDir: 'dist'`、`outExtensions` | [option-output-directory](references/option-output-directory.md) |
| 类型声明 | `dts: true`、`dts: { sourcemap, compilerOptions, vue }` | [option-dts](references/option-dts.md) |
| 目标环境 | `target: 'es2020'`、`target: 'esnext'` | [option-target](references/option-target.md) |
| 平台 | `platform: 'node'`、`platform: 'browser'` | [option-platform](references/option-platform.md) |
| Tree shaking | `treeshake: true`、自定义选项 | [option-tree-shaking](references/option-tree-shaking.md) |
| 压缩 | `minify: true`、`minify: 'dce-only'` | [option-minification](references/option-minification.md) |
| 源映射 | `sourcemap: true`、`'inline'`、`'hidden'` | [option-sourcemap](references/option-sourcemap.md) |
| 监视模式 | `watch: true`、监视选项 | [option-watch-mode](references/option-watch-mode.md) |
| 清理 | `clean: true`、清理模式 | [option-cleaning](references/option-cleaning.md) |
| 日志级别 | `logLevel: 'silent'`、`failOnWarn: false` | [option-log-level](references/option-log-level.md) |

## 依赖处理

| 功能 | 用法 | 参考 |
|---------|-------|-----------|
| 从不打包 | `deps: { neverBundle: ['react', /^@myorg\//] }` | [option-dependencies](references/option-dependencies.md) |
| 始终打包 | `deps: { alwaysBundle: ['dep-to-bundle'] }` | [option-dependencies](references/option-dependencies.md) |
| 仅打包 | `deps: { onlyBundle: ['cac', 'bumpp'] }` - 白名单 | [option-dependencies](references/option-dependencies.md) |
| 跳过 node_modules | `deps: { skipNodeModulesBundle: true }` | [option-dependencies](references/option-dependencies.md) |
| 自动外部化 | 自动 peer/依赖外部化 | [option-dependencies](references/option-dependencies.md) |

## 输出增强

| 功能 | 用法 | 参考 |
|---------|-------|-----------|
| Shims | `shims: true` - 添加 ESM/CJS 兼容性 | [option-shims](references/option-shims.md) |
| CJS 默认值 | `cjsDefault: true`（默认）/ `false` | [option-cjs-default](references/option-cjs-default.md) |
| 包导出 | `exports: true` - 自动生成 exports 字段 | [option-package-exports](references/option-package-exports.md) |
| CSS 处理 | **[实验性]** `css: { ... }` — 完整管道，包含预处理器、Lightning CSS、PostCSS、代码分割；需要 `@tsdown/css` | [option-css](references/option-css.md) |
| CSS 注入 | `css: { inject: true }` — 在 JS 输出中保留 CSS 导入 | [option-css](references/option-css.md) |
| 不打包模式 | `unbundle: true` - 保留目录结构 | [option-unbundle](references/option-unbundle.md) |
| 根目录 | `root: 'src'` - 控制输出目录映射 | [option-root](references/option-root.md) |
| 可执行文件 | **[实验性]** `exe: true` - 打包为独立可执行文件，通过 `@tsdown/exe` 跨平台 | [option-exe](references/option-exe.md) |
| 包验证 | `publint: true`、`attw: true` - 验证包 | [option-lint](references/option-lint.md) |

## 框架和运行时支持

| 框架 | 指南 | 参考 |
|-----------|-------|-----------|
| React | JSX 转换、React Compiler | [recipe-react](references/recipe-react.md) |
| Vue | SFC 支持、JSX | [recipe-vue](references/recipe-vue.md) |
| Solid | SolidJS JSX 转换 | [recipe-solid](references/recipe-solid.md) |
| Svelte | Svelte 组件库（推荐源码分发） | [recipe-svelte](references/recipe-svelte.md) |
| WASM | 通过 `rolldown-plugin-wasm` 支持 WebAssembly 模块 | [recipe-wasm](references/recipe-wasm.md) |

## 常见模式

### 基本库打包

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  clean: true,
})
```

### 多入口点

```ts
export default defineConfig({
  entry: {
    index: 'src/index.ts',
    utils: 'src/utils.ts',
    cli: 'src/cli.ts',
  },
  format: ['esm', 'cjs'],
  dts: true,
})
```

### 浏览器库（IIFE/UMD）

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['iife'],
  globalName: 'MyLib',
  platform: 'browser',
  minify: true,
})
```

### React 组件库

```ts
export default defineConfig({
  entry: ['src/index.tsx'],
  format: ['esm', 'cjs'],
  dts: true,
  deps: {
    neverBundle: ['react', 'react-dom'],
  },
  inputOptions: {
    jsx: { runtime: 'automatic' },
  },
})
```

### 保留目录结构

```ts
export default defineConfig({
  entry: ['src/**/*.ts', '!**/*.test.ts'],
  unbundle: true, // 保留文件结构
  format: ['esm'],
  dts: true,
})
```

### CI 感知配置

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  failOnWarn: 'ci-only',  // 选择加入：在 CI 中因警告而失败
  publint: 'ci-only',
  attw: 'ci-only',
})
```

### WASM 支持

```ts
import { wasm } from 'rolldown-plugin-wasm'
import { defineConfig } from 'tsdown'

export default defineConfig({
  entry: ['src/index.ts'],
  plugins: [wasm()],
})
```

### 带有 CSS 和 Sass 的库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  target: 'chrome100',
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@use "src/styles/variables" as *;`,
      },
    },
  },
})
```

### 独立可执行文件

```ts
export default defineConfig({
  entry: ['src/cli.ts'],
  exe: true,
})
```

### 跨平台可执行文件（需要 `@tsdown/exe`）

```ts
export default defineConfig({
  entry: ['src/cli.ts'],
  exe: {
    targets: [
      { platform: 'linux', arch: 'x64', nodeVersion: '25.7.0' },
      { platform: 'darwin', arch: 'arm64', nodeVersion: '25.7.0' },
      { platform: 'win', arch: 'x64', nodeVersion: '25.7.0' },
    ],
  },
})
```

### 带有钩子的高级用法

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  hooks: {
    'build:before': async (context) => {
      console.log('构建中...')
    },
    'build:done': async (context) => {
      console.log('构建完成！')
    },
  },
})
```

## 配置功能

### 多个配置

导出数组以进行多个构建配置：

```ts
export default defineConfig([
  {
    entry: ['src/index.ts'],
    format: ['esm', 'cjs'],
    dts: true,
  },
  {
    entry: ['src/cli.ts'],
    format: ['esm'],
    platform: 'node',
  },
])
```

### 条件配置

使用函数进行动态配置：

```ts
export default defineConfig((options) => {
  const isDev = options.watch
  return {
    entry: ['src/index.ts'],
    format: ['esm', 'cjs'],
    minify: !isDev,
    sourcemap: isDev,
  }
})
```

### 工作区/Monorepo

使用 glob 模式构建多个包：

```ts
export default defineConfig({
  workspace: 'packages/*',
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
})
```

## CLI 快速参考

```bash
# 基本命令
tsdown                          # 构建一次
tsdown --watch                  # 监视模式
tsdown --config custom.ts       # 自定义配置
npx tsdown-migrate              # 从 tsup 迁移

# 输出选项
tsdown --format esm,cjs        # 多种格式
tsdown -d lib                  # 自定义输出目录（--out-dir）
tsdown --minify                # 启用压缩
tsdown --dts                   # 生成声明
tsdown --exe                   # 打包为独立可执行文件
tsdown --unbundle              # 无打包模式

# 入口选项
tsdown src/index.ts            # 单个入口
tsdown src/*.ts                # Glob 模式
tsdown src/a.ts src/b.ts       # 多个入口

# 工作区 / Monorepo
tsdown -W                      # 启用工作区模式
tsdown -W -F my-package        # 过滤特定包
tsdown --filter /^pkg-/        # 按正则表达式过滤

# 开发
tsdown --watch                 # 监视模式
tsdown --sourcemap             # 生成源映射
tsdown --clean                 # 清理输出目录
tsdown --from-vite             # 重用 Vite 配置
tsdown --tsconfig tsconfig.build.json  # 自定义 tsconfig
```

## 最佳实践

1. **始终生成类型声明**用于 TypeScript 库：
   ```ts
   { dts: true }
   ```

2. **外部化依赖**以避免打包不必要的代码：
   ```ts
   { deps: { neverBundle: [/^react/, /^@myorg\//] } }
   ```

3. **使用 tree shaking**以获得最佳包大小：
   ```ts
   { treeshake: true }
   ```

4. **启用压缩**用于生产构建：
   ```ts
   { minify: true }
   ```

5. **添加 shims**以获得更好的 ESM/CJS 兼容性：
   ```ts
   { shims: true }  // 添加 __dirname、__filename 等。
   ```

6. **自动生成 package.json exports**：
   ```ts
   { exports: true }  // 创建正确的 exports 字段
   ```

7. **在开发期间使用监视模式**：
   ```bash
   tsdown --watch
   ```

8. **为包含许多文件的实用工具保留结构**：
   ```ts
   { unbundle: true }  // 保留目录结构
   ```

9. **在发布前在 CI 中验证包**：
   ```ts
   { publint: 'ci-only', attw: 'ci-only' }
   ```

## 资源

- 文档：https://tsdown.dev
- GitHub：https://github.com/rolldown/tsdown
- Rolldown：https://rolldown.rs
- 迁移指南：https://tsdown.dev/guide/migrate-from-tsup
