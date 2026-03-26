# 从 tsup 迁移

从 tsup 切换到 tsdown 的迁移指南。

## 概述

tsdown 基于 Rolldown（基于 Rust）vs tsup 的 esbuild，在保持兼容性的同时提供更快、更强大的打包。

## 自动迁移

### 单个包

```bash
npx tsdown-migrate
```

### Monorepo

```bash
# 使用 glob 模式
npx tsdown-migrate packages/*

# 多个目录
npx tsdown-migrate packages/foo packages/bar
```

### 迁移选项

- `[...dirs]` - 要迁移的目录（支持 glob）
- `--dry-run` 或 `-d` - 预览更改而不修改文件

**重要：** 运行迁移之前提交您的更改。

## 主要区别

### 默认值

| 选项 | tsup | tsdown |
|--------|------|--------|
| `format` | `['cjs']` | `['esm']` |
| `clean` | `false` | `true` |
| `dts` | `false` | 如果 package.json 中有 `types`/`typings` 则自动启用 |
| `target` | 手动 | 从 package.json 中的 `engines.node` 自动读取 |

### tsdown 中的新功能

#### Node 协议控制

```ts
export default defineConfig({
  nodeProtocol: true,      // 添加 node: 前缀（fs → node:fs）
  nodeProtocol: 'strip',   // 移除 node: 前缀（node:fs → fs）
  nodeProtocol: false,     // 保持原样（默认）
})
```

#### 更好的工作区支持

```ts
export default defineConfig({
  workspace: 'packages/*',  // 构建所有包
})
```

## 迁移清单

1. **备份您的代码** - 提交所有更改
2. **运行迁移工具** - `npx tsdown-migrate`
3. **审查更改** - 检查修改的配置文件
4. **更新脚本** - 在 package.json 中将 `tsup` 更改为 `tsdown`
5. **测试构建** - 运行 `pnpm build` 进行验证
6. **调整配置** - 根据您的需求进行微调

## 常见迁移模式

### 基本库

**之前（tsup）：**
```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['cjs', 'esm'],
  dts: true,
})
```

**之后（tsdown）：**
```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],  // ESM 现在是默认值
  dts: true,
  clean: true,  // 现在默认启用
})
```

### 使用自定义目标

**之前（tsup）：**
```ts
export default defineConfig({
  entry: ['src/index.ts'],
  target: 'es2020',
})
```

**之后（tsdown）：**
```ts
export default defineConfig({
  entry: ['src/index.ts'],
  // target 从 package.json engines.node 自动读取
  // 或显式覆盖：
  target: 'es2020',
})
```

### CLI 脚本

**之前（package.json）：**
```json
{
  "scripts": {
    "build": "tsup",
    "dev": "tsup --watch"
  }
}
```

**之后（package.json）：**
```json
{
  "scripts": {
    "build": "tsdown",
    "dev": "tsdown --watch"
  }
}
```

## 功能兼容性

### 支持的 tsup 功能

大多数 tsup 功能都受支持：
- ✅ 多个入口点
- ✅ 多种格式（ESM、CJS、IIFE、UMD）
- ✅ TypeScript 声明
- ✅ 源映射
- ✅ 压缩
- ✅ 监视模式
- ✅ 外部依赖项
- ✅ Tree shaking
- ✅ Shims
- ✅ 插件（Rollup 兼容）

### 缺失的功能

某些 tsup 功能尚不可用。请查看 [GitHub issues](https://github.com/rolldown/tsdown/issues) 了解状态并请求功能。

## 故障排除

### 迁移后构建失败

1. **检查 Node.js 版本** - 需要 Node.js 20.19+
2. **安装 TypeScript** - DTS 生成需要
3. **审查配置更改** - 确保格式和选项正确
4. **检查依赖项** - 验证所有依赖项都已安装

### 不同的输出

- **格式顺序** - tsdown 默认为 ESM 优先
- **清理行为** - tsdown 默认清理 outDir
- **目标** - tsdown 从 package.json 自动检测

### 性能问题

tsdown 应该比 tsup 更快。如果不是：
1. 启用 `isolatedDeclarations` 以加快 DTS 生成
2. 检查是否有大型依赖项被打包
3. 如有必要，使用 `skipNodeModulesBundle`

## 获取帮助

- [GitHub Issues](https://github.com/rolldown/tsdown/issues) - 报告错误或请求功能
- [文档](https://tsdown.dev) - 完整文档
- [迁移工具](https://github.com/rolldown/tsdown/tree/main/packages/tsdown-migrate) - 源代码

## 致谢

tsdown 深受 tsup 启发，并集成了其代码库的部分内容。感谢 [@egoist](https://github.com/egoist) 和 tsup 社区。
