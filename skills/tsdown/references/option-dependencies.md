# 依赖项

控制依赖项如何被打包或外部化。

## 概述

tsdown 智能地处理依赖项，以保持您的库轻量级，同时确保包含所有必要的代码。

## 默认行为

### 自动外部化

这些**默认情况下不打包**：

- **`dependencies`** - 随您的包自动安装
- **`peerDependencies`** - 用户必须手动安装

### 有条件打包

这些**仅在导入时打包**：

- **`devDependencies`** - 仅在源代码中实际使用时
- **幻影依赖** - 在 node_modules 中但不在 package.json 中

## 配置选项

所有依赖项选项都归在 `deps` 字段下：

```ts
export default defineConfig({
  deps: {
    neverBundle: ['react', /^@myorg\//],
    alwaysBundle: ['some-package'],
    onlyBundle: ['cac', 'bumpp'],
    skipNodeModulesBundle: true,
  },
})
```

### `deps.neverBundle`

将依赖项标记为外部（不打包）：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  deps: {
    neverBundle: [
      'react',              // 单个包
      'react-dom',
      /^@myorg\//,         // 正则表达式模式（所有 @myorg/* 包）
      /^lodash/,           // 所有 lodash 包
    ],
  },
})
```

### `deps.alwaysBundle`

强制打包依赖项：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  deps: {
    alwaysBundle: [
      'some-package',      // 即使在 dependencies 中也打包
      'vendor-lib',
    ],
  },
})
```

### `deps.onlyBundle`

允许从 node_modules 打包的依赖项白名单。如果打包了任何未列出的依赖项，则抛出错误：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  deps: {
    onlyBundle: [
      'cac',               // 允许打包 cac
      'bumpp',             // 允许打包 bumpp
      /^my-utils/,         // 支持正则表达式模式
    ],
  },
})
```

**行为：**
- **数组** (`['cac', /^my-/]`)：仅匹配的依赖项可以打包。其他依赖项报错。
- **`false`**：抑制关于打包依赖项的所有警告。
- **未设置**（默认）：如果打包了任何 node_modules 依赖项，则发出警告。

**注意：** 在列表中包括所有子依赖项，而不仅仅是顶层导入。

### `deps.skipNodeModulesBundle`

跳过解析和打包所有 node_modules：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  deps: {
    skipNodeModulesBundle: true,
  },
})
```

**结果：** 不解析或打包来自 node_modules 的依赖项。

**注意：** 不能与 `alwaysBundle` 一起使用。

## 常见模式

### React 组件库

```ts
export default defineConfig({
  entry: ['src/index.tsx'],
  format: ['esm', 'cjs'],
  deps: {
    neverBundle: [
      'react',
      'react-dom',
      /^react\//,          // react/jsx-runtime 等
    ],
  },
  dts: true,
})
```

### 带有共享依赖的实用程序库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  deps: {
    alwaysBundle: ['lodash-es'],
  },
  dts: true,
})
```

### Monorepo 包

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  deps: {
    neverBundle: [
      /^@mycompany\//,     // 不打包其他工作区包
    ],
  },
  dts: true,
})
```

### CLI 工具（打包所有内容）

```ts
export default defineConfig({
  entry: ['src/cli.ts'],
  format: ['esm'],
  platform: 'node',
  deps: {
    alwaysBundle: [/.*/],
  },
  shims: true,
})
```

### 带有特定外部化的库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  deps: {
    neverBundle: [
      'vue',
      '@vue/runtime-core',
      '@vue/reactivity',
    ],
  },
  dts: true,
})
```

## 声明文件

`.d.ts` 文件的依赖项处理遵循与 JavaScript 相同的规则。

### 复杂类型解析

使用 TypeScript 解析器处理复杂的第三方类型：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  dts: {
    resolver: 'tsc',     // 使用 TypeScript 解析器而不是 Oxc
  },
})
```

**何时使用 `tsc` 解析器：**
- `@types/*` 包中具有非标准命名的类型（例如，`@types/babel__generator`）
- 复杂的类型依赖项
- 默认 Oxc 解析器的问题

**权衡：** `tsc` 较慢但更兼容。

## CLI 用法

### 不打包

```bash
tsdown --deps.never-bundle react --deps.never-bundle react-dom
tsdown --deps.never-bundle '/^@myorg\/.*/'
```

### 跳过 Node 模块

```bash
tsdown --deps.skip-node-modules-bundle
```

## 从已弃用选项迁移

| 已弃用选项 | 新选项 |
|---|---|
| `external` | `deps.neverBundle` |
| `noExternal` | `deps.alwaysBundle` |
| `inlineOnly` | `deps.onlyBundle` |
| `deps.onlyAllowBundle` | `deps.onlyBundle` |
| `skipNodeModulesBundle` | `deps.skipNodeModulesBundle` |

## 按用例示例

### 框架组件

```ts
// 不打包框架
export default defineConfig({
  deps: {
    neverBundle: ['vue', 'react', 'solid-js', 'svelte'],
  },
})
```

### 独立应用程序

```ts
// 打包所有内容
export default defineConfig({
  deps: {
    alwaysBundle: [/.*/],
  },
})
```

### 共享库

```ts
// 仅打包特定的实用程序
export default defineConfig({
  deps: {
    neverBundle: [/.*/],        // 默认外部化
    alwaysBundle: ['tiny-utils'], // 除了这个
  },
})
```

### Monorepo 包

```ts
// 外部化工作区包，打包实用程序
export default defineConfig({
  deps: {
    neverBundle: [
      /^@workspace\//,     // 其他工作区包
      'react',
      'react-dom',
    ],
    alwaysBundle: [
      'lodash-es',         // 打包实用程序库
    ],
  },
})
```

## 故障排除

### 意外打包依赖项

检查它是否在 `devDependencies` 中并被导入。移动到 `dependencies`：

```json
{
  "dependencies": {
    "should-be-external": "^1.0.0"
  }
}
```

或显式外部化：

```ts
export default defineConfig({
  deps: {
    neverBundle: ['should-be-external'],
  },
})
```

### 运行时缺少依赖项

确保它在 `dependencies` 或 `peerDependencies` 中：

```json
{
  "dependencies": {
    "needed-package": "^1.0.0"
  }
}
```

或打包它：

```ts
export default defineConfig({
  deps: {
    alwaysBundle: ['needed-package'],
  },
})
```

### 类型解析错误

使用 TypeScript 解析器处理复杂类型：

```ts
export default defineConfig({
  dts: {
    resolver: 'tsc',
  },
})
```

## 总结

**默认行为：**
- `dependencies` & `peerDependencies` → 外部化
- `devDependencies` & 幻影依赖 → 如果导入则打包

**覆盖（在 `deps` 下）：**
- `neverBundle` → 强制外部化
- `alwaysBundle` → 强制打包
- `onlyBundle` → 打包依赖项白名单
- `skipNodeModulesBundle` → 跳过所有 node_modules

**声明文件：**
- 与 JavaScript 相同的打包逻辑
- 对复杂类型使用 `resolver: 'tsc'`

## 提示

1. **保持依赖项外部化**用于库
2. **打包所有内容**用于独立 CLI
3. **使用正则表达式模式**用于命名空间包
4. **检查包大小**以验证外部化/打包拆分
5. **使用全新安装测试**以捕获缺少的依赖项
6. **仅在需要时使用 tsc 解析器**（较慢）

## 相关选项

- [外部化](option-dependencies.md) - 此页面
- [平台](option-platform.md) - 运行时环境
- [输出格式](option-output-format.md) - 模块格式
- [DTS](option-dts.md) - 类型声明
