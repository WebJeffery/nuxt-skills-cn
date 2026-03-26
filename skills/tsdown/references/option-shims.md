# Shims

在 ESM 和 CommonJS 模块系统之间添加兼容性。

## 概述

Shims 提供小块代码来桥接 CommonJS (CJS) 和 ECMAScript 模块 (ESM) 之间的差距，实现跨模块系统兼容性。

## Shims 提供的内容

### ESM 输出（启用时）

使用 `shims: true`，将 CommonJS 变量添加到 ESM：

- `__dirname` - 当前目录路径
- `__filename` - 当前文件路径

### ESM 输出（自动）

在 Node.js 上的 ESM 中使用 `require` 时始终添加：

- 通过 `createRequire(import.meta.url)` 的 `require` 函数

### CJS 输出（自动）

始终添加到 CommonJS 输出：

- `import.meta.url`
- `import.meta.dirname`
- `import.meta.filename`

## 用法

### CLI

```bash
tsdown --shims
```

### 配置文件

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'],
  shims: true,
})
```

## 生成的代码

### 带有 Shims 的 ESM

**源代码：**
```ts
console.log(__dirname)
console.log(__filename)
```

**输出（shims: true）：**
```js
import { fileURLToPath } from 'node:url'
import { dirname } from 'node:path'

const __filename = fileURLToPath(import.meta.url)
const __dirname = dirname(__filename)

console.log(__dirname)
console.log(__filename)
```

### 带有 require 的 ESM

**源代码：**
```ts
const mod = require('some-module')
```

**输出（在 Node.js 上自动）：**
```js
import { createRequire } from 'node:module'
const require = createRequire(import.meta.url)

const mod = require('some-module')
```

### 带有 import.meta 的 CJS

**源代码：**
```ts
console.log(import.meta.url)
console.log(import.meta.dirname)
```

**输出（自动）：**
```js
const import_meta = {
  url: require('url').pathToFileURL(__filename).toString(),
  dirname: __dirname,
  filename: __filename
}

console.log(import_meta.url)
console.log(import_meta.dirname)
```

## 常见模式

### Node.js CLI 工具

```ts
export default defineConfig({
  entry: ['src/cli.ts'],
  format: ['esm'],
  platform: 'node',
  shims: true,  // 添加 __dirname, __filename
})
```

### 双格式库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  platform: 'node',
  shims: true,  // ESM 获得 __dirname/__filename
                // CJS 获得 import.meta.*（自动）
})
```

### 服务器端代码

```ts
export default defineConfig({
  entry: ['src/server.ts'],
  format: ['esm'],
  platform: 'node',
  shims: true,
  deps: {
    neverBundle: [/.*/],  // 外部化所有依赖项
  },
})
```

### 文件系统操作

```ts
// 源代码
import { readFileSync } from 'fs'
import { join } from 'path'

// 读取相对于当前模块的文件
const content = readFileSync(join(__dirname, 'data.json'), 'utf-8')
```

```ts
// tsdown 配置
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'],
  shims: true,  // 启用 __dirname
})
```

## 何时使用 Shims

### 使用 `shims: true` 时：

- ✅ 构建 Node.js 工具/CLI
- ✅ 代码使用 `__dirname` 或 `__filename`
- ✅ 需要相对于模块的文件系统操作
- ✅ 从 CommonJS 迁移到 ESM
- ✅ 需要跨格式兼容性

### 不需要 shims 时：

- ❌ 仅浏览器代码
- ❌ 没有文件系统操作
- ❌ 仅使用 `import.meta.url`
- ❌ 没有 CJS 变量的纯 ESM

## 性能影响

### 运行时开销

Shims 添加最小的运行时开销：

```js
// 启用 shims 时添加到输出
import { fileURLToPath } from 'node:url'
import { dirname } from 'node:path'

const __filename = fileURLToPath(import.meta.url)
const __dirname = dirname(__filename)
```

### Tree Shaking

如果未使用 `__dirname` 或 `__filename`，它们会在打包期间自动删除（无开销）。

## 平台注意事项

### Node.js 平台

```ts
export default defineConfig({
  platform: 'node',
  format: ['esm'],
  shims: true,  // 推荐 Node.js 使用
})
```

- 自动添加 `require` shim
- 使用 `shims: true` 时可用 `__dirname` 和 `__filename`

### 浏览器平台

```ts
export default defineConfig({
  platform: 'browser',
  format: ['esm'],
  shims: false,  // 浏览器不需要
})
```

- 不需要 shims（没有 Node.js 变量）
- 如果使用 Node.js API 会发出警告

### 中立平台

```ts
export default defineConfig({
  platform: 'neutral',
  format: ['esm'],
  shims: false,  // 避免平台特定代码
})
```

- 避免使用 shims 以获得最大可移植性

## CLI 示例

```bash
# 启用 shims
tsdown --shims

# Node.js 的 ESM 配合 shims
tsdown --format esm --platform node --shims

# 双格式配合 shims
tsdown --format esm --format cjs --shims
```

## 故障排除

### `__dirname is not defined`

启用 shims：

```ts
export default defineConfig({
  shims: true,
})
```

### ESM 中的 `require is not defined`

在 Node.js 平台上自动。如果不工作：

```ts
export default defineConfig({
  platform: 'node',  // 确保 Node.js 平台
})
```

### CJS 中的 Import.meta 不工作

自动 - 无需配置。如果仍然失败，检查输出格式：

```ts
export default defineConfig({
  format: ['cjs'],  // 自动添加 shims
})
```

## 提示

1. **为 Node.js 工具启用** - 对 CLI 和服务器使用 `shims: true`
2. **为浏览器跳过** - 浏览器代码不需要
3. **未使用时无开销** - 自动 tree-shaken
4. **自动 require shim** - ESM 中的 `require` 无需配置
5. **CJS shims 自动** - CJS 中始终可用 `import.meta.*`

## 相关选项

- [平台](option-platform.md) - 运行时环境
- [输出格式](option-output-format.md) - 模块格式
- [目标](option-target.md) - 语法转换
