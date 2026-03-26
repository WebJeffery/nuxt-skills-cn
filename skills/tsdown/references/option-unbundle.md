# Unbundle 模式

在输出中保留源目录结构。

## 概述

Unbundle 模式（也称为"无包"或"仅转译"）输出镜像源结构的文件，而不是将所有内容打包到单个文件中。每个源文件都是单独编译的，具有一对一的映射。

## 基本用法

### CLI

```bash
tsdown --unbundle
```

### 配置文件

```ts
export default defineConfig({
  entry: ['src/**/*.ts', '!**/*.test.ts'],
  unbundle: true,
})
```

## 工作原理

### 源结构

```
src/
├── index.ts
├── utils/
│   ├── helper.ts
│   └── format.ts
└── components/
    └── button.ts
```

### 使用 Unbundle

**配置：**
```ts
export default defineConfig({
  entry: ['src/index.ts'],
  unbundle: true,
})
```

**输出：**
```
dist/
├── index.mjs
├── utils/
│   ├── helper.mjs
│   └── format.mjs
└── components/
    └── button.mjs
```

所有导入的文件都是单独输出的，保留结构。

### 不使用 Unbundle（默认）

**输出：**
```
dist/
└── index.mjs  (所有代码打包在一起)
```

## 何时使用

### 使用 Unbundle 当：

✅ 构建具有共享工具的 monorepo 包
✅ 用户需要导入单个模块
✅ 想要清晰的源到输出映射
✅ 具有许多独立工具的库
✅ 调试需要跟踪特定文件
✅ 增量构建以加快开发

### 使用标准打包当：

❌ 单入口点应用程序
❌ 想要优化包大小
❌ 需要激进的 tree shaking
❌ 创建 IIFE/UMD 包
❌ 直接部署到浏览器

## 常见模式

### 工具库

```ts
export default defineConfig({
  entry: ['src/**/*.ts', '!**/*.test.ts'],
  format: ['esm', 'cjs'],
  unbundle: true,
  dts: true,
})
```

**好处：**
- 用户只导入他们需要的内容
- Tree shaking 仍然在用户的构建中工作
- 清晰的模块边界

**用法：**
```ts
// 用户可以导入特定工具
import { helper } from 'my-lib/utils/helper'
import { Button } from 'my-lib/components/button'
```

### Monorepo 共享包

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'],
  unbundle: true,
  outDir: 'dist',
})
```

### 仅 TypeScript 编译

```ts
export default defineConfig({
  entry: ['src/**/*.ts'],
  format: ['esm'],
  unbundle: true,
  minify: false,
  treeshake: false,
  dts: true,
})
```

纯 TypeScript 到 JavaScript 转换。

### 开发模式

```ts
export default defineConfig((options) => ({
  entry: ['src/**/*.ts'],
  unbundle: options.watch,  // 仅在开发时 unbundle
  minify: !options.watch,
}))
```

开发期间快速重建，为生产优化。

## 使用入口模式

### 包含/排除

```ts
export default defineConfig({
  entry: [
    'src/**/*.ts',
    '!**/*.test.ts',
    '!**/*.spec.ts',
    '!**/fixtures/**',
  ],
  unbundle: true,
})
```

### 多个入口点

```ts
export default defineConfig({
  entry: {
    index: 'src/index.ts',
    cli: 'src/cli.ts',
  },
  unbundle: true,
})
```

入口文件和所有导入都保留。

## 输出控制

### 自定义扩展

```ts
export default defineConfig({
  entry: ['src/**/*.ts'],
  unbundle: true,
  outExtensions: () => ({ js: '.js' }),
})
```

### 保留目录

```ts
export default defineConfig({
  entry: ['src/**/*.ts'],
  unbundle: true,
  outDir: 'lib',
})
```

**输出：**
```
lib/
├── index.js
├── utils/
│   └── helper.js
└── components/
    └── button.js
```

## Package.json 设置

```json
{
  "name": "my-library",
  "type": "module",
  "main": "./dist/index.js",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": "./dist/index.js",
    "./utils/*": "./dist/utils/*.js",
    "./components/*": "./dist/components/*.js"
  },
  "files": ["dist"]
}
```

或使用 `exports: true` 自动生成。

## 比较

| 特性 | 打包 | Unbundle |
|---------|---------|-----------|
| 输出文件 | 少 | 多 |
| 文件大小 | 更小 | 更大 |
| 构建速度 | 更慢 | 更快 |
| Tree shaking | 构建时 | 用户的构建 |
| 源映射 | 复杂 | 简单 |
| 模块导入 | 仅入口 | 任何模块 |
| 开发重建 | 更慢 | 更快 |

## 性能

### 构建速度

Unbundle 通常更快：
- 无打包开销
- 并行文件处理
- 可能的增量构建

### 包大小

Unbundle 产生更大的输出：
- 每个文件都有自己的开销
- 无跨模块优化
- 用户的打包器处理最终优化

## 提示

1. **使用 glob 模式**用于多个文件
2. **在开发中启用**以加快重建
3. **让用户打包**以进行生产优化
4. **保留结构**用于工具/组件
5. **与 DTS 结合**用于类型定义
6. **与 monorepos 一起使用**用于共享代码

## 故障排除

### 文件太多

- 调整入口模式
- 排除不必要的文件
- 使用特定的入口点

### 缺少文件

- 检查入口模式
- 验证文件是否被导入
- 查找排除的模式

### 导入路径错误

- 检查相对路径
- 验证输出结构
- 更新 package.json exports

## CLI 示例

```bash
# 启用 unbundle
tsdown --unbundle

# 使用特定入口
tsdown src/**/*.ts --unbundle

# 使用其他选项
tsdown --unbundle --format esm --dts
```

## 相关选项

- [根目录](option-root.md) - 控制输出目录映射
- [入口点](option-entry.md) - 入口模式
- [输出目录](option-output-directory.md) - 输出位置
- [输出格式](option-output-format.md) - 模块格式
- [DTS](option-dts.md) - 类型声明