# 目标环境

为目标环境配置 JavaScript 语法转换。

## 概述

`target` 选项控制哪些 JavaScript 特性被降级（转换为旧语法）以实现兼容性。

**重要：** 仅影响语法转换，不影响运行时 polyfills。

## 默认行为

tsdown 从 `package.json` 自动读取：

```json
// package.json
{
  "engines": {
    "node": ">=18.0.0"
  }
}
```

自动将 `target` 设置为 `node18.0.0`。

如果不存在 `engines.node` 字段，则行为类似于 `target: false`（无转换）。

## 禁用转换

设置为 `false` 以保留现代语法：

```ts
export default defineConfig({
  target: false,
})
```

**结果：**
- 无 JavaScript 降级
- 保留现代特性（可选链 `?.`、空值合并 `??` 等）

**在以下情况下使用：**
- 针对现代环境
- 在其他地方处理转换
- 构建库以供进一步处理

## 设置目标

### CLI

```bash
# 单个目标
tsdown --target es2020
tsdown --target node20

# 多个目标
tsdown --target chrome100 --target node20.18

# 禁用
tsdown --no-target
```

### 配置文件

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  target: 'es2020',
})
```

### 多个目标

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  target: ['chrome100', 'safari15', 'node18'],
})
```

## 支持的目标

### ECMAScript 版本

- `es2015`、`es2016`、`es2017`、`es2018`、`es2019`、`es2020`、`es2021`、`es2022`、`es2023`、`esnext`

### 浏览器版本

- `chrome100`、`safari18`、`firefox110`、`edge100` 等

### Node.js 版本

- `node16`、`node18`、`node20`、`node20.18` 等

## 示例

### 现代浏览器

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'],
  target: ['chrome100', 'safari15', 'firefox100'],
})
```

### Node.js 库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  target: 'node18',
})
```

### 传统支持

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'],
  target: 'es2015',  // 最大兼容性
})
```

### 每种格式目标

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: {
    esm: {
      target: 'es2020',
    },
    cjs: {
      target: 'node16',
    },
  },
})
```

## 装饰器

### 传统装饰器（Stage 2）

在 `tsconfig.json` 中启用：

```json
{
  "compilerOptions": {
    "experimentalDecorators": true
  }
}
```

### Stage 3 装饰器

tsdown/Rolldown/Oxc 目前**不支持**。

请参阅 [oxc issue #9170](https://github.com/oxc-project/oxc/issues/9170)。

## 常见模式

### 通用库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  target: 'es2020',  // 广泛兼容性
})
```

### 仅现代库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'],
  target: false,  // 无转换
})
```

### 浏览器组件

```ts
export default defineConfig({
  entry: ['src/index.tsx'],
  format: ['esm'],
  target: ['chrome100', 'safari15', 'firefox100'],
  platform: 'browser',
})
```

## CSS 目标

当安装了 `@tsdown/css` 并设置了浏览器目标时，CSS 语法也会自动降级：

```ts
export default defineConfig({
  target: 'chrome108',  // CSS 嵌套将被展平
})
```

完整的 CSS 配置选项请参阅 [CSS](option-css.md)。

## 提示

1. **让 tsdown 从 package.json 自动检测**（如果可能）
2. **使用 `false`**用于仅现代构建
3. **指定多个目标**以获得更广泛的兼容性
4. **使用传统装饰器**配合 `experimentalDecorators`
5. **安装 `@tsdown/css`**用于 CSS 支持和语法降级
6. **在目标环境中测试输出**

## 相关选项

- [平台](option-platform.md) - 运行时环境
- [输出格式](option-output-format.md) - 模块格式
- [压缩](option-minification.md) - 代码优化
- [CSS](option-css.md) - CSS 处理和预处理器
