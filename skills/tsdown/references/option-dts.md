# TypeScript 声明文件

为您的库生成 `.d.ts` 类型声明文件。

## 概述

tsdown 使用 [rolldown-plugin-dts](https://github.com/sxzz/rolldown-plugin-dts) 来生成和打包 TypeScript 声明文件。

**要求：**
- 必须在项目中安装 TypeScript

## 启用 DTS 生成

### 自动启用

如果 `package.json` 包含以下内容，DTS 生成将**自动启用**：
- `types` 字段，或
- `typings` 字段

### 手动启用

#### CLI

```bash
tsdown --dts
```

#### 配置文件

```ts
export default defineConfig({
  dts: true,
})
```

## 性能

### 使用 `isolatedDeclarations`（推荐）

**极快** - 使用 oxc-transform 进行生成。

```json
// tsconfig.json
{
  "compilerOptions": {
    "isolatedDeclarations": true
  }
}
```

### 不使用 `isolatedDeclarations`

回退到 TypeScript 编译器。可靠但较慢。

## 声明映射

将 `.d.ts` 文件映射回原始 `.ts` 源文件（对 monorepos 有用）。

### 在 tsconfig.json 中启用

```json
{
  "compilerOptions": {
    "declarationMap": true
  }
}
```

### 在 tsdown 配置中启用

```ts
export default defineConfig({
  dts: {
    sourcemap: true,
  },
})
```

## 高级选项

### 自定义编译器选项

覆盖 TypeScript 编译器选项：

```ts
export default defineConfig({
  dts: {
    compilerOptions: {
      removeComments: false,
    },
  },
})
```

## 构建过程

- **ESM 格式**：在同一构建中生成 `.js` 和 `.d.ts` 文件
- **CJS 格式**：为 `.d.ts` 文件使用单独的构建过程

## 常见模式

### 基本库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
})
```

输出：
- `dist/index.mjs`
- `dist/index.cjs`
- `dist/index.d.ts`

### 多个入口点

```ts
export default defineConfig({
  entry: {
    index: 'src/index.ts',
    utils: 'src/utils.ts',
  },
  format: ['esm', 'cjs'],
  dts: true,
})
```

输出：
- `dist/index.mjs`、`dist/index.cjs`、`dist/index.d.ts`
- `dist/utils.mjs`、`dist/utils.cjs`、`dist/utils.d.ts`

### Monorepo 支持

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: {
    sourcemap: true, // 启用声明映射
  },
})
```

### 快速构建（独立声明）

```json
// tsconfig.json
{
  "compilerOptions": {
    "isolatedDeclarations": true,
    "declaration": true,
    "declarationMap": true
  }
}
```

```ts
// tsdown.config.ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true, // 将使用快速的 oxc-transform
})
```

## 故障排除

### 缺少类型

确保已安装 TypeScript：

```bash
pnpm add -D typescript
```

### 生成缓慢

在 `tsconfig.json` 中启用 `isolatedDeclarations` 以加快构建。

### 声明错误

检查所有导出是否都有显式类型（`isolatedDeclarations` 所需）。

### 报告问题

对于 DTS 特定问题，请报告到 [rolldown-plugin-dts](https://github.com/sxzz/rolldown-plugin-dts/issues)。

### Vue 支持

启用 Vue 组件类型生成（需要 `vue-tsc`）：

```ts
export default defineConfig({
  dts: {
    vue: true,
  },
})
```

### Oxc Transform

控制 Oxc 用于声明生成的使用：

```ts
export default defineConfig({
  dts: {
    oxc: true,  // 使用 oxc-transform（快速，需要 isolatedDeclarations）
  },
})
```

### 自定义 TSConfig

为 DTS 生成指定不同的 tsconfig：

```ts
export default defineConfig({
  dts: {
    tsconfig: './tsconfig.build.json',
  },
})
```

## 可用的 DTS 选项

| 选项 | 类型 | 描述 |
|--------|------|-------------|
| `sourcemap` | `boolean` | 生成声明源映射 |
| `compilerOptions` | `object` | 覆盖 TypeScript 编译器选项 |
| `vue` | `boolean` | 启用 Vue 类型生成（需要 vue-tsc） |
| `oxc` | `boolean` | 使用 oxc-transform 进行快速生成 |
| `tsconfig` | `string` | tsconfig 文件的路径 |
| `resolver` | `'oxc' \| 'tsc'` | 模块解析器：`'oxc'`（默认，快速）或 `'tsc'`（更兼容） |
| `cjsDefault` | `boolean` | CJS 默认导出处理 |
| `sideEffects` | `boolean` | 在声明中保留副作用 |

## 提示

1. **始终启用 DTS**用于 TypeScript 库
2. **使用 isolatedDeclarations**以加快构建
3. **在 monorepos 中启用声明映射**
4. **确保所有导出都有显式类型**
5. **安装 TypeScript**作为开发依赖

## 相关选项

- [入口点](option-entry.md) - 配置入口点
- [输出格式](option-output-format.md) - 多种输出格式
- [目标](option-target.md) - JavaScript 版本
