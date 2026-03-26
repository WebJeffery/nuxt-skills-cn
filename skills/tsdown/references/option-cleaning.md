# 输出目录清理

控制构建前如何清理输出目录。

## 概述

默认情况下，tsdown 在每次构建前**清理输出目录**以删除先前构建中的过时文件。

## 基本用法

### CLI

```bash
# 启用清理（默认）
tsdown

# 禁用清理
tsdown --no-clean
```

### 配置文件

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  clean: true,  // 默认
})
```

## 行为

### 启用清理（默认）

每次构建前：
1. `outDir` 中的所有文件都被删除
2. 全新构建从空目录开始
3. 仅保留当前构建输出

**好处：**
- 没有过时文件
- 可预测的输出
- 每次构建都是干净的

### 不启用清理

构建输出添加到现有文件：

```ts
export default defineConfig({
  clean: false,
})
```

**在以下情况下使用：**
- 多次构建到同一目录
- 增量构建
- 保留其他文件
- 监视模式（更快的重新构建）

## 常见模式

### 生产构建

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  clean: true,  // 确保干净的输出
  minify: true,
})
```

### 开发模式

```ts
export default defineConfig((options) => ({
  entry: ['src/index.ts'],
  clean: !options.watch,  // 在监视模式下不清理
  sourcemap: options.watch,
}))
```

### 多次构建

```ts
export default defineConfig([
  {
    entry: ['src/index.ts'],
    outDir: 'dist',
    clean: true,  // 清理一次
  },
  {
    entry: ['src/cli.ts'],
    outDir: 'dist',
    clean: false,  // 不清理，添加到同一目录
  },
])
```

### Monorepo 包

```ts
export default defineConfig({
  workspace: 'packages/*',
  entry: ['src/index.ts'],
  clean: true,  // 清理每个包的 dist
})
```

### 保留静态文件

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  clean: false,  // 保留手动添加的文件
  outDir: 'dist',
})

// 首先手动复制文件
// 然后运行 tsdown --no-clean
```

## 清理模式

### 选择性清理

```ts
import { rmSync } from 'fs'

export default defineConfig({
  clean: false,  // 禁用自动清理
  hooks: {
    'build:prepare': () => {
      // 自定义清理逻辑
      rmSync('dist/*.js', { force: true })
      // 保留其他文件
    },
  },
})
```

### 清理特定目录

```ts
export default defineConfig({
  clean: false,
  hooks: {
    'build:prepare': async () => {
      const { rm } = await import('fs/promises')
      // 仅清理特定子目录
      await rm('dist/esm', { recursive: true, force: true })
      await rm('dist/cjs', { recursive: true, force: true })
      // 保留 dist/types
    },
  },
})
```

## 监视模式行为

在监视模式下，清理行为很重要：

### 仅在首次构建时清理

```ts
export default defineConfig((options) => ({
  entry: ['src/index.ts'],
  watch: options.watch,
  clean: !options.watch,  // 仅清理初始构建
}))
```

**结果：**
- 首次构建：清理
- 后续重新构建：增量

### 始终清理

```ts
export default defineConfig({
  watch: true,
  clean: true,  // 每次重新构建都清理
})
```

**权衡：** 重新构建较慢，但输出始终是新的。

## 提示

1. **保持启用**用于生产构建
2. **在监视模式下禁用**以加快重新构建
3. **小心使用多个配置**与清理
4. **自定义清理逻辑**通过 hooks（如果需要）
5. **谨慎** - 清理会删除 outDir 中的所有文件
6. **测试清理** - 确保没有重要文件丢失

## 故障排除

### 重要文件被删除

- 不要将非构建文件放在 outDir 中
- 为静态文件使用单独的目录
- 禁用清理并手动管理

### 输出中有过时文件

- 启用清理：`clean: true`
- 或在构建前手动删除

### 监视中重新构建缓慢

- 在监视模式下禁用清理
- 使用增量构建

## CLI 示例

```bash
# 默认（启用清理）
tsdown

# 禁用清理
tsdown --no-clean

# 不清理的监视模式
tsdown --watch --no-clean

# 多种格式并清理
tsdown --format esm,cjs --clean
```

## 示例

### 安全的生产构建

```bash
# 构建前清理
rm -rf dist
tsdown --clean
```

### 增量开发

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  watch: true,
  clean: false,  // 更快的重新构建
  sourcemap: true,
})
```

### 多阶段构建

```ts
// 阶段 1：清理并构建主文件
export default defineConfig([
  {
    entry: ['src/index.ts'],
    outDir: 'dist',
    clean: true,
  },
  {
    entry: ['src/utils.ts'],
    outDir: 'dist',
    clean: false,  // 添加到同一目录
  },
])
```

## 相关选项

- [输出目录](option-output-directory.md) - 配置 outDir
- [监视模式](option-watch-mode.md) - 开发工作流
- [Hooks](advanced-hooks.md) - 自定义清理逻辑
- [入口点](option-entry.md) - 入口点
