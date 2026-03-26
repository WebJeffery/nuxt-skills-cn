---
name: library-development
description: 使用 tsdown 构建和发布 TypeScript 库。用于创建 npm 包、配置库打包或设置 package.json 导出时。
---

# 库开发

| 方面 | 选择 |
|--------|--------|
| 打包器 | tsdown |
| 输出 | 仅纯 ESM（无 CJS） |
| DTS | 通过 tsdown 生成 |
| 导出 | 通过 tsdown 自动生成 |

## tsdown 配置

使用启用了这些选项的 tsdown：

```ts
// tsdown.config.ts
import { defineConfig } from 'tsdown'

export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'],
  dts: true,
  exports: true,
})
```

| 选项 | 值 | 用途 |
|--------|-------|---------|
| `format` | `['esm']` | 纯 ESM，无 CommonJS |
| `dts` | `true` | 生成 `.d.ts` 文件 |
| `exports` | `true` | 自动更新 `package.json` 中的 `exports` 字段 |

### 多个入口点

```ts
export default defineConfig({
  entry: [
    'src/index.ts',
    'src/utils.ts',
  ],
  format: ['esm'],
  dts: true,
  exports: true,
})
```

当运行 `tsdown` 时，`exports: true` 选项会自动生成 `package.json` 中的 `exports` 字段。

---

## package.json

纯 ESM 库的必需字段：

```json
{
  "type": "module",
  "main": "./dist/index.mjs",
  "module": "./dist/index.mjs",
  "types": "./dist/index.d.mts",
  "files": ["dist"],
  "scripts": {
    "build": "tsdown",
    "prepack": "pnpm build",
    "test": "vitest",
    "release": "bumpp -r"
  }
}
```

当 `exports: true` 时，`exports` 字段由 tsdown 管理。

### prepack 脚本

对于每个公共包，在 `scripts` 中添加 `"prepack": "pnpm build"`。这确保包在发布之前自动构建（例如，当运行 `npm publish` 或 `pnpm publish` 时）。这防止意外发布过时或缺失的构建产物。
