# 包验证（publint & attw）

在发布前验证您的包配置和类型声明。

## 概述

tsdown 与 [publint](https://publint.dev/) 和 [Are the types wrong?](https://arethetypeswrong.github.io/) (attw) 集成，以捕获常见的打包问题。两者都是可选依赖项。

## 安装

```bash
# 仅 publint
npm install -D publint

# 仅 attw
npm install -D @arethetypeswrong/core

# 两者
npm install -D publint @arethetypeswrong/core
```

## publint

检查 `package.json` 字段（`exports`、`main`、`module`、`types`）是否与您的实际输出文件匹配。

### 启用

```ts
export default defineConfig({
  publint: true,
})
```

### 配置

```ts
export default defineConfig({
  publint: {
    level: 'error', // 'warning' | 'error' | 'suggestion'
  },
})
```

### CLI

```bash
tsdown --publint
```

## attw (Are the types wrong?)

验证 TypeScript 声明在不同模块解析策略（`node10`、`node16`、`bundler`）下是否正确。

### 启用

```ts
export default defineConfig({
  attw: true,
})
```

### 配置

```ts
export default defineConfig({
  attw: {
    profile: 'node16',   // 'strict' | 'node16' | 'esm-only'
    level: 'error',       // 'warn' | 'error'
    ignoreRules: ['false-cjs', 'cjs-resolves-to-esm'],
  },
})
```

### 配置文件

| 配置文件 | 描述 |
|---------|-------------|
| `strict` | 要求所有解析都通过（默认）|
| `node16` | 忽略 `node10` 解析失败 |
| `esm-only` | 忽略 `node10` 和 `node16-cjs` 解析失败 |

### 忽略规则

使用 `ignoreRules` 抑制特定问题类型：

| 规则 | 描述 |
|------|-------------|
| `no-resolution` | 无法解析模块 |
| `untyped-resolution` | 解析成功但没有类型 |
| `false-cjs` | 类型指示 CJS 但实现是 ESM |
| `false-esm` | 类型指示 ESM 但实现是 CJS |
| `cjs-resolves-to-esm` | CJS 解析指向 ESM 模块 |
| `fallback-condition` | 使用了回退/通配符条件 |
| `cjs-only-exports-default` | CJS 模块仅导出默认值 |
| `named-exports` | 类型和实现之间的命名导出不匹配 |
| `false-export-default` | 类型声明不存在的默认导出 |
| `missing-export-equals` | 类型缺少 CJS 的 `export =` |
| `unexpected-module-syntax` | 文件使用意外的模块语法 |
| `internal-resolution-error` | 类型检查中的内部解析错误 |

### CLI

```bash
tsdown --attw
```

## CI 集成

两个工具都支持 CI 感知选项：

```ts
export default defineConfig({
  publint: 'ci-only',
  attw: {
    enabled: 'ci-only',
    profile: 'node16',
    level: 'error',
  },
})
```

两个工具都需要在项目目录中有 `package.json`。

## 相关选项

- [CI 环境](advanced-ci.md) - CI 感知选项详细信息
- [包导出](option-package-exports.md) - 自动生成 exports 字段