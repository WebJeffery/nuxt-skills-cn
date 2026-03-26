# Tree Shaking

从包中删除未使用的代码。

## 概述

Tree shaking 从最终包中消除死代码（未使用的导出），减小大小并提高性能。

**默认：** 已启用

## 基本用法

### CLI

```bash
# Tree shaking 已启用（默认）
tsdown

# 禁用 tree shaking
tsdown --no-treeshake
```

### 配置文件

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  treeshake: true,  // 默认
})
```

## 工作原理

### 使用 Tree Shaking

**源代码：**
```ts
// src/util.ts
export function unused() {
  console.log("I'm unused")
}

export function hello(x: number) {
  console.log('Hello World', x)
}

// src/index.ts
import { hello } from './util'
hello(1)
```

**输出：**
```js
// dist/index.mjs
function hello(x) {
  console.log('Hello World', x)
}
hello(1)
```

`unused()` 函数被删除，因为它从未被导入。

### 不使用 Tree Shaking

**输出：**
```js
// dist/index.mjs
function unused() {
  console.log("I'm unused")
}

function hello(x) {
  console.log('Hello World', x)
}
hello(1)
```

包含所有代码，即使未使用。

## 高级配置

### 启用（默认）

```ts
export default defineConfig({
  treeshake: true,
})
```

使用 Rolldown 的默认 tree shaking。

### 自定义选项

```ts
export default defineConfig({
  treeshake: {
    moduleSideEffects: false,
    propertyReadSideEffects: false,
    unknownGlobalSideEffects: false,
  },
})
```

请参阅 [Rolldown 文档](https://rolldown.rs/reference/config-options#treeshake) 了解所有选项。

### 禁用

```ts
export default defineConfig({
  treeshake: false,
})
```

## 副作用

### Package.json sideEffects

在包中声明副作用：

```json
{
  "sideEffects": false
}
```

或指定具有副作用的文件：

```json
{
  "sideEffects": ["*.css", "src/polyfills.ts"]
}
```

### 模块副作用

```ts
export default defineConfig({
  treeshake: {
    moduleSideEffects: (id) => {
      // 为 polyfills 保留副作用
      return id.includes('polyfill')
    },
  },
})
```

## 常见模式

### 生产构建

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  treeshake: true,
  minify: true,
})
```

### 开发构建

```ts
export default defineConfig((options) => ({
  entry: ['src/index.ts'],
  treeshake: !options.watch,  // 在开发中禁用
}))
```

### 带有副作用的库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  treeshake: {
    moduleSideEffects: (id) => {
      return (
        id.includes('.css') ||
        id.includes('polyfill') ||
        id.includes('side-effect')
      )
    },
  },
})
```

### 实用程序库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'],
  treeshake: true,
  dts: true,
})
```

用户可以仅导入他们需要的内容：
```ts
import { onlyWhatINeed } from 'my-utils'
```

## 好处

### 更小的包

- 仅包含导入的代码
- 删除未使用的函数、类、变量
- 减少下载大小

### 更好的性能

- 更少的代码需要解析
- 更快的执行
- 改进的加载时间

### 更清洁的输出

- 生产中没有死代码
- 更容易调试
- 更好的可维护性

## 何时禁用

### 调试

在开发期间查看所有代码：

```ts
export default defineConfig((options) => ({
  treeshake: !options.watch,
}))
```

### 副作用代码

具有全局副作用的代码：

```ts
// 这有副作用
window.myGlobal = {}

export function setup() {
  // ...
}
```

禁用 tree shaking 或标记副作用：

```json
{
  "sideEffects": true
}
```

### 测试

包含所有代码以进行覆盖率：

```ts
export default defineConfig({
  treeshake: false,
})
```

## 提示

1. **保持启用**用于生产构建
2. **在 package.json 中标记副作用**
3. **与压缩结合**以获得最佳结果
4. **测试 tree shaking** - 验证未使用的代码被删除
5. **在需要时禁用**用于调试
6. **纯函数**更容易进行 tree shaking

## 故障排除

### 代码仍然被包含

- 检查副作用
- 验证导入是否为 ES 模块
- 确保代码实际上未被使用
- 检查 package.json 中的 `sideEffects`

### 运行时缺少代码

- 代码有副作用但标记为无副作用
- 设置 `sideEffects: true` 或列出特定文件

### 意外行为

- 模块具有未声明的副作用
- 尝试禁用 tree shaking 以隔离问题

## 示例

### 纯实用程序函数

```ts
// utils.ts - 非常适合 tree shaking
export function add(a, b) {
  return a + b
}

export function multiply(a, b) {
  return a * b
}

// 仅导入 'add' = 仅打包 'add'
import { add } from './utils'
```

### 带有副作用

```ts
// polyfill.ts - 有副作用
if (!Array.prototype.at) {
  Array.prototype.at = function(index) {
    // polyfill 实现
  }
}

export {} // 需要导出一些内容
```

```json
{
  "sideEffects": ["src/polyfill.ts"]
}
```

## 相关选项

- [压缩](option-minification.md) - 代码压缩
- [目标](option-target.md) - 语法转换
- [依赖项](option-dependencies.md) - 外部包
- [输出格式](option-output-format.md) - 模块格式
