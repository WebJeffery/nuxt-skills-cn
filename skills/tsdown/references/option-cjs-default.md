# CJS 默认导出

控制在 CommonJS 输出中如何处理默认导出。

## 概述

`cjsDefault` 选项在生成 CommonJS 模块时提高兼容性。启用时（默认），只有单个默认导出的模块使用 `module.exports = ...` 而不是 `exports.default = ...`。

## 类型

```ts
cjsDefault?: boolean  // 默认：true
```

## 基本用法

### 启用（默认）

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['cjs'],
  cjsDefault: true,  // 默认行为
})
```

### 禁用

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['cjs'],
  cjsDefault: false,
})
```

## 工作原理

### 使用 `cjsDefault: true`（默认）

当您的模块**只有单个默认导出**时，tsdown 转换：

**源代码：**
```ts
// src/index.ts
export default function greet() {
  console.log('Hello, world!')
}
```

**生成的 CJS：**
```js
// dist/index.cjs
function greet() {
  console.log('Hello, world!')
}
module.exports = greet
```

**生成的声明：**
```ts
// dist/index.d.cts
declare function greet(): void
export = greet
```

这允许使用者直接使用 `const greet = require('your-module')`。

### 使用 `cjsDefault: false`

默认导出保持为 `exports.default`：

```js
// dist/index.cjs
function greet() {
  console.log('Hello, world!')
}
exports.default = greet
```

使用者需要 `require('your-module').default`。

## 何时禁用

- 当您的模块同时具有默认导出和命名导出时
- 当您需要一致的 `exports.default` 行为时
- 当使用者始终使用 ESM 导入时

## 提示

1. **保持启用**用于大多数库（默认 `true`）
2. **禁用**如果您同时具有默认导出和命名导出并且需要一致的行为
3. **测试 CJS 使用者**以验证兼容性

## 相关选项

- [输出格式](option-output-format.md) - 模块格式
- [Shims](option-shims.md) - ESM/CJS 兼容性