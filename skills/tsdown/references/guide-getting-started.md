# 入门指南

首次安装和使用 tsdown 的快速指南。

## 安装

将 tsdown 安装为开发依赖：

```bash
pnpm add -D tsdown

# 如果不使用 isolatedDeclarations，可选择安装 TypeScript
pnpm add -D typescript
```

**要求：**
- Node.js 20.19 或更高版本
- 对 Deno 和 Bun 的实验性支持

## 快速开始模板

使用 `create-tsdown` CLI 进行即时设置：

```bash
pnpm create tsdown@latest
```

提供以下模板：
- 纯 TypeScript 库
- React 组件库
- Vue 组件库
- 即用型配置

## 首次打包

### 1. 创建源文件

```ts
// src/index.ts
import { hello } from './hello.ts'
hello()

// src/hello.ts
export function hello() {
  console.log('Hello tsdown!')
}
```

### 2. 创建配置文件

```ts
// tsdown.config.ts
import { defineConfig } from 'tsdown'

export default defineConfig({
  entry: ['./src/index.ts'],
})
```

### 3. 运行构建

```bash
./node_modules/.bin/tsdown
```

输出：`dist/index.mjs`

### 4. 测试输出

```bash
node dist/index.mjs
# 输出：Hello tsdown!
```

## 添加到 npm 脚本

```json
{
  "scripts": {
    "build": "tsdown"
  }
}
```

运行：

```bash
pnpm build
```

## CLI 命令

```bash
# 检查版本
tsdown --version

# 查看帮助
tsdown --help

# 使用监视模式构建
tsdown --watch

# 使用特定格式构建
tsdown --format esm,cjs

# 生成类型声明
tsdown --dts
```

## 基本配置

### TypeScript 库（ESM + CJS）

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  clean: true,
})
```

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

### 多个入口点

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

## 使用插件

添加 Rolldown、Rollup 或 Unplugin 插件：

```ts
import SomePlugin from 'some-plugin'

export default defineConfig({
  entry: ['src/index.ts'],
  plugins: [SomePlugin()],
})
```

## 监视模式

在文件更改时启用自动重新构建：

```bash
tsdown --watch
# 或
tsdown -w
```

## 下一步

- 使用 glob 模式配置[入口点](option-entry.md)
- 设置[多个输出格式](option-output-format.md)
- 启用[类型声明生成](option-dts.md)
- 探索[插件](advanced-plugins.md)以获得扩展功能
- 如果从 tsup 迁移，请阅读[迁移指南](guide-migrate-from-tsup.md)
