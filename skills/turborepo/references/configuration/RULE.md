# turbo.json 配置概述

Turborepo 的配置参考。完整文档：https://turborepo.dev/docs/reference/configuration

## 文件位置

根 `turbo.json` 位于仓库根目录，与根 `package.json` 相邻：

```
my-monorepo/
├── turbo.json        # 根配置
├── package.json
└── packages/
    └── web/
        ├── turbo.json  # 包配置（可选）
        └── package.json
```

## 始终优先使用包任务而不是根任务

**始终使用包任务。仅当无法使用包任务成功时才使用根任务。**

包任务启用并行化、单独缓存和过滤。在每个包的 `package.json` 中定义脚本：

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

```json
// 根 package.json - 委托给 turbo
{
  "scripts": {
    "build": "turbo run build",
    "lint": "turbo run lint",
    "test": "turbo run test",
    "typecheck": "turbo run typecheck"
  }
}
```

当你运行 `turbo run lint` 时，Turborepo 查找所有具有 `lint` 脚本的包并**并行**运行它们。

**根任务是后备方案**，而不是默认值。仅将它们用于真正无法按包运行的任务（例如，仓库级别的 CI 脚本、工作区范围的配置生成）。

```json
// 避免：根中的任务逻辑会破坏并行化
{
  "scripts": {
    "lint": "eslint apps/web && eslint apps/api && eslint packages/ui"
  }
}
```

## 基本结构

```json
{
  "$schema": "https://v2-8-18-canary-7.turborepo.dev/schema.json",
  "globalEnv": ["CI"],
  "globalDependencies": ["tsconfig.json"],
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

`$schema` 键启用 IDE 自动完成和验证。

## 配置部分

**全局选项** - 影响所有任务的设置：

- `globalEnv`、`globalDependencies`、`globalPassThroughEnv`
- `cacheDir`、`daemon`、`envMode`、`ui`、`remoteCache`

**任务定义** - `tasks` 对象中的每个任务设置：

- `dependsOn`、`outputs`、`inputs`、`env`
- `cache`、`persistent`、`interactive`、`outputLogs`

## 包配置

在单个包中使用 `turbo.json` 来覆盖根设置：

```json
// packages/web/turbo.json
{
  "extends": ["//"],
  "tasks": {
    "build": {
      "outputs": [".next/**", "!.next/cache/**"]
    }
  }
}
```

`"extends": ["//"]` 是必需的 - 它引用根配置。

**何时使用包配置：**

- 框架特定的输出（Next.js、Vite 等）
- 特定于包的环境变量
- 特定包的不同缓存规则
- 将框架配置保持在框架代码附近

### 从其他包扩展

你可以从配置包而不是仅从根扩展：

```json
// packages/web/turbo.json
{
  "extends": ["//", "@repo/turbo-config"]
}
```

### 使用 `$TURBO_EXTENDS$` 添加到继承的数组

默认情况下，包配置中的数组字段**替换**根值。使用 `$TURBO_EXTENDS$` 来**追加**：

```json
// 根 turbo.json
{
  "tasks": {
    "build": {
      "outputs": ["dist/**"]
    }
  }
}
```

```json
// packages/web/turbo.json
{
  "extends": ["//"],
  "tasks": {
    "build": {
      // 从根继承 "dist/**"，添加 ".next/**"
      "outputs": ["$TURBO_EXTENDS$", ".next/**", "!.next/cache/**"]
    }
  }
}
```

没有 `$TURBO_EXTENDS$`，输出将只有 `[".next/**", "!.next/cache/**"]`。

**适用于：**

- `dependsOn`
- `env`
- `inputs`
- `outputs`
- `passThroughEnv`
- `with`

### 从包中排除任务

使用 `extends: false` 从包中排除任务：

```json
// packages/ui/turbo.json
{
  "extends": ["//"],
  "tasks": {
    "e2e": {
      "extends": false // UI 包没有 e2e 测试
    }
  }
}
```

## 使用 `turbo.jsonc` 添加注释

使用 `turbo.jsonc` 扩展名添加注释并支持 IDE：

```jsonc
// turbo.jsonc
{
  "tasks": {
    "build": {
      // Next.js 输出
      "outputs": [".next/**", "!.next/cache/**"]
    }
  }
}
```