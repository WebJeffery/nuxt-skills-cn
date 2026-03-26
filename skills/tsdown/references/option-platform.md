# 平台

打包代码的目标运行时环境。

## 概述

平台确定运行时环境，并影响模块解析、内置处理和优化。

## 可用平台

| 平台 | 运行时 | 内置 | 用例 |
|----------|---------|-----------|----------|
| `node` | Node.js（默认） | 自动解析 | 服务器端、CLI、工具 |
| `browser` | Web 浏览器 | 如果使用则警告 | 前端应用程序 |
| `neutral` | 平台无关 | 无假设 | 通用库 |

## 使用

### CLI

```bash
tsdown --platform node     # 默认
tsdown --platform browser
tsdown --platform neutral
```

### 配置文件

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  platform: 'browser',
})
```

## 平台详情

### Node 平台

服务器端和工具的**默认平台**。

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  platform: 'node',
})
```

**特性：**
- Node.js 内置（fs、path 等）自动解析
- 针对 Node.js 运行时优化
- 与 Deno 和 Bun 兼容
- 默认 mainFields：`['main', 'module']`

### 浏览器平台

用于在浏览器中运行的 Web 应用程序。

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  platform: 'browser',
  format: ['esm'],
})
```

**特性：**
- 如果使用 Node.js 内置则发出警告
- 可能需要 Node API 的 polyfills
- 针对浏览器环境优化
- 默认 mainFields：`['browser', 'module', 'main']`

### 中立平台

用于通用库的平台无关。

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  platform: 'neutral',
  format: ['esm'],
})
```

**特性：**
- 无运行时假设
- 无自动内置解析
- 仅依赖 `exports` 字段
- 默认 mainFields：`[]`
- 对运行时行为的完全控制

## CJS 格式限制

**CJS 格式始终使用 `node` 平台**，无法更改。

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['cjs'],
  platform: 'browser',  // 对于 CJS 被忽略
})
```

详情请参阅 [rolldown PR #4693](https://github.com/rolldown/rolldown/pull/4693#issuecomment-2912229545)。

## 模块解析

### Main Fields

不同平台检查不同的 `package.json` 字段：

| 平台 | mainFields | 优先级 |
|----------|------------|----------|
| `node` | `['main', 'module']` | main → module |
| `browser` | `['browser', 'module', 'main']` | browser → module → main |
| `neutral` | `[]` | 仅 `exports` 字段 |

### 中立平台解析

使用 `neutral` 时，没有 `exports` 字段的包可能无法解析：

```
Help: The "main" field here was ignored. Main fields must be configured
explicitly when using "neutral" platform.
```

**解决方案：** 显式配置 mainFields：

```ts
export default defineConfig({
  platform: 'neutral',
  inputOptions: {
    resolve: {
      mainFields: ['module', 'main'],
    },
  },
})
```

## 常见模式

### Node.js CLI 工具

```ts
export default defineConfig({
  entry: ['src/cli.ts'],
  format: ['esm'],
  platform: 'node',
  shims: true,
})
```

### 浏览器库（IIFE）

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['iife'],
  platform: 'browser',
  globalName: 'MyLib',
  minify: true,
})
```

### 通用库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'],
  platform: 'neutral',
  inputOptions: {
    resolve: {
      mainFields: ['module', 'main'],
    },
  },
})
```

### React 组件库

```ts
export default defineConfig({
  entry: ['src/index.tsx'],
  format: ['esm', 'cjs'],
  platform: 'browser',
  deps: {
    neverBundle: ['react', 'react-dom'],
  },
})
```

### Node.js + 浏览器构建

```ts
export default defineConfig([
  {
    entry: ['src/index.ts'],
    format: ['esm', 'cjs'],
    platform: 'node',
  },
  {
    entry: ['src/browser.ts'],
    format: ['esm'],
    platform: 'browser',
  },
])
```

## 故障排除

### Node 内置警告（浏览器）

在浏览器构建中使用 Node.js API 时：

```
Warning: Module "fs" has been externalized for browser compatibility
```

**解决方案：**
1. 如果不是仅浏览器，使用 platform: 'node'
2. 为 Node API 添加 polyfills
3. 在浏览器代码中避免 Node.js 内置
4. 使用 platform: 'neutral' 并小心管理依赖项

### 模块解析问题（中立）

当包无法使用 `neutral` 解析时：

```ts
export default defineConfig({
  platform: 'neutral',
  inputOptions: {
    resolve: {
      mainFields: ['module', 'browser', 'main'],
      conditions: ['import', 'require'],
    },
  },
})
```

## 提示

1. **使用 `node`**用于服务器端和 CLI（默认）
2. **使用 `browser`**用于前端应用程序
3. **使用 `neutral`**用于通用库
4. **使用中立平台时配置 mainFields**
5. **CJS 始终是 node** - 其他平台使用 ESM
6. **在目标环境中测试**以验证兼容性

## 相关选项

- [输出格式](option-output-format.md) - 模块格式
- [目标](option-target.md) - JavaScript 版本
- [Shims](option-shims.md) - ESM/CJS 兼容性
- [依赖项](option-dependencies.md) - 外部包
