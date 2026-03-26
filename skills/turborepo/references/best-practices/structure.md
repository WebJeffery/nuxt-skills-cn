# 仓库结构

构建 Turborepo monorepo 的详细指南。

## 工作区配置

### pnpm（推荐）

```yaml
# pnpm-workspace.yaml
packages:
  - "apps/*"
  - "packages/*"
```

### npm/yarn/bun

```json
// package.json
{
  "workspaces": ["apps/*", "packages/*"]
}
```

## 根 package.json

```json
{
  "name": "my-monorepo",
  "private": true,
  "packageManager": "pnpm@9.0.0",
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev",
    "lint": "turbo run lint",
    "test": "turbo run test"
  },
  "devDependencies": {
    "turbo": "latest"
  }
}
```

关键点：

- `private: true` - 防止意外发布
- `packageManager` - 强制执行一致的包管理器版本
- **脚本仅委派给 `turbo run`** - 这里没有实际的构建逻辑！
- 最少的 devDependencies（只有 turbo 和仓库工具）

## 始终优先使用包任务

**始终使用包任务。只有在无法使用包任务成功时才使用根任务。**

```json
// packages/web/package.json
{
  "scripts": {
    "build": "next build",
    "lint": "eslint .",
    "test": "vitest",
    "typecheck": "tsc --noEmit"
  }
}

// packages/api/package.json
{
  "scripts": {
    "build": "tsc",
    "lint": "eslint .",
    "test": "vitest",
    "typecheck": "tsc --noEmit"
  }
}
```

包任务使 Turborepo 能够：

1. **并行化** - 同时运行 `web#lint` 和 `api#lint`
2. **单独缓存** - 每个包的任务输出单独缓存
3. **精确过滤** - 运行 `turbo run test --filter=web` 仅针对一个包

**根任务是后备**，用于真正无法按包运行的任务：

```json
// 除非必要，否则避免 - 顺序执行，未并行化，无法过滤
{
  "scripts": {
    "lint": "eslint apps/web && eslint apps/api && eslint packages/ui"
  }
}
```

## 根 turbo.json

```json
{
  "$schema": "https://v2-8-18-canary-7.turborepo.dev/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", ".next/**", "!.next/cache/**"]
    },
    "lint": {},
    "test": {
      "dependsOn": ["build"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

## 目录组织

### 分组包

您可以通过添加更多工作区路径来对包进行分组：

```yaml
# pnpm-workspace.yaml
packages:
  - "apps/*"
  - "packages/*"
  - "packages/config/*" # 分组配置
  - "packages/features/*" # 功能包
```

这允许：

```
packages/
├── ui/
├── utils/
├── config/
│   ├── eslint/
│   ├── typescript/
│   └── tailwind/
└── features/
    ├── auth/
    └── payments/
```

### 不应该做什么

```yaml
# 错误：嵌套通配符会导致行为不明确
packages:
  - "packages/**" # 不要这样做！
```

## 包结构

### 最低要求文件

```
packages/ui/
├── package.json    # 必需：使其成为一个包
├── src/            # 源代码
│   └── button.tsx
└── tsconfig.json   # TypeScript 配置（如果使用 TS）
```

### package.json 要求

```json
{
  "name": "@repo/ui", // 唯一的、命名空间的名称
  "version": "0.0.0", // 版本（内部包可以是 0.0.0）
  "private": true, // 防止意外发布
  "exports": {
    // 入口点
    "./button": "./src/button.tsx"
  }
}
```

## TypeScript 配置

### 共享基础配置

创建共享的 TypeScript 配置包：

```
packages/
└── typescript-config/
    ├── package.json
    ├── base.json
    ├── nextjs.json
    └── library.json
```

```json
// packages/typescript-config/base.json
{
  "compilerOptions": {
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "module": "ESNext",
    "target": "ES2022"
  }
}
```

### 在包中扩展

```json
// packages/ui/tsconfig.json
{
  "extends": "@repo/typescript-config/library.json",
  "compilerOptions": {
    "outDir": "dist",
    "rootDir": "src"
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

### 没有根 tsconfig.json

您可能不需要在工作区根目录中使用 `tsconfig.json`。每个包都应该有自己的配置，从共享配置包扩展而来。

## ESLint 配置

### 共享配置包

```
packages/
└── eslint-config/
    ├── package.json
    ├── base.js
    ├── next.js
    └── library.js
```

```json
// packages/eslint-config/package.json
{
  "name": "@repo/eslint-config",
  "exports": {
    "./base": "./base.js",
    "./next": "./next.js",
    "./library": "./library.js"
  }
}
```

### 在包中使用

```js
// apps/web/.eslintrc.js
module.exports = {
  extends: ["@repo/eslint-config/next"]
};
```

## 锁文件

锁文件对于以下情况是**必需的**：

- 可重现的构建
- Turborepo 了解包依赖关系
- 缓存正确性

没有锁文件，您将看到不可预测的行为。
