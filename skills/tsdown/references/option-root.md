# 根目录

指定输入文件的根目录，用于输出结构映射。

## 概述

`root` 选项类似于 TypeScript 的 `rootDir`。它确定入口文件路径如何映射到输出路径。默认情况下，tsdown 将根计算为所有入口文件的公共基本目录。显式设置 `root` 可以让您覆盖此行为。

## 基本用法

### CLI

```bash
tsdown --root src
```

### 配置文件

```ts
export default defineConfig({
  entry: ['src/index.ts', 'src/utils/helper.ts'],
  root: 'src',
})
```

## 工作原理

### 默认

给定入口 `src/index.ts` 和 `src/utils/helper.ts`，公共基本目录是 `src/`：

```
dist/
├── index.js
└── utils/
    └── helper.js
```

### 使用 `root: '.'`

将根设置为项目目录会保留 `src/` 前缀：

```
dist/
└── src/
    ├── index.js
    └── utils/
        └── helper.js
```

## 影响内容

1. **入口名称解析** - 数组入口路径相对于 `root` 计算以生成输出文件名
2. **Unbundle 模式** - 用作 `preserveModulesRoot`，控制 `unbundle: true` 时的输出结构

## 何时使用

- 自动计算的公共基本目录未产生所需的输出结构
- 需要在输出路径中包含或排除目录前缀
- Unbundle 模式需要特定的目录映射

## 常见模式

### 保留 `src/` 前缀的库

```ts
export default defineConfig({
  entry: ['src/**/*.ts', '!**/*.test.ts'],
  root: '.',
  unbundle: true,
})
```

### Monorepo 包

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  root: 'src',
  unbundle: true,
})
```

## 相关选项

- [Unbundle](option-unbundle.md) - 保留目录结构
- [入口点](option-entry.md) - 入口点配置
- [输出目录](option-output-directory.md) - 输出位置