---
name: turborepo
description: |
  Turborepo monorepo 构建系统指南。触发条件：turbo.json、任务管道、
  dependsOn、缓存、远程缓存、"turbo" CLI、--filter、--affected、CI 优化、环境
  变量、内部包、monorepo 结构/最佳实践和边界。

  当用户执行以下操作时使用：配置任务/工作流/管道、创建包、设置
  monorepo、在应用之间共享代码、运行更改/受影响的包、调试缓存，
  或拥有 apps/packages 目录。
---

# Turborepo 技能

JavaScript/TypeScript monorepos 的构建系统。Turborepo 缓存任务输出并根据依赖图并行运行任务。

## 重要提示：包任务，而非根任务

**不要创建根任务。始终创建包任务。**

创建任务/脚本/管道时，您必须：

1. 将脚本添加到每个相关包的 `package.json`
2. 在根 `turbo.json` 中注册任务
3. 根 `package.json` 仅通过 `turbo run <task>` 委派

**不要**将任务逻辑放在根 `package.json` 中。这会破坏 Turborepo 的并行化。

```json
// 正确做法：每个包中的脚本
// apps/web/package.json
{ "scripts": { "build": "next build", "lint": "eslint .", "test": "vitest" } }

// apps/api/package.json
{ "scripts": { "build": "tsc", "lint": "eslint .", "test": "vitest" } }

// packages/ui/package.json
{ "scripts": { "build": "tsc", "lint": "eslint .", "test": "vitest" } }
```

```json
// turbo.json - 注册任务
{
  "tasks": {
    "build": { "dependsOn": ["^build"], "outputs": ["dist/**"] },
    "lint": {},
    "test": { "dependsOn": ["build"] }
  }
}
```

```json
// 根 package.json - 仅委派，无任务逻辑
{
  "scripts": {
    "build": "turbo run build",
    "lint": "turbo run lint",
    "test": "turbo run test"
  }
}
```

```json
// 错误做法 - 破坏并行化
// 根 package.json
{
  "scripts": {
    "build": "cd apps/web && next build && cd ../api && tsc",
    "lint": "eslint apps/ packages/",
    "test": "vitest"
  }
}
```

根任务（`//#taskname`）仅用于真正无法存在于包中的任务（罕见）。

## 次要规则：`turbo run` vs `turbo`

**当命令写入代码时，始终使用 `turbo run`：**

```json
// package.json - 始终使用 "turbo run"
{
  "scripts": {
    "build": "turbo run build"
  }
}
```

```yaml
# CI 工作流 - 始终使用 "turbo run"
- run: turbo run build --affected
```

**简写 `turbo <tasks>` 仅用于人类或代理直接输入的一次性终端命令。** 绝不要将 `turbo build` 写入 package.json、CI 或脚本。

## 快速决策树

### "我需要配置一个任务"

```
配置任务？
├─ 定义任务依赖 → references/configuration/tasks.md
├─ Lint/检查类型（并行 + 缓存）→ 使用 Transit Nodes 模式（见下文）
├─ 指定构建输出 → references/configuration/tasks.md#outputs
├─ 处理环境变量 → references/environment/RULE.md
├─ 设置 dev/watch 任务 → references/configuration/tasks.md#persistent
├─ 包特定配置 → references/configuration/RULE.md#package-configurations
└─ 全局设置（cacheDir、daemon）→ references/configuration/global-options.md
```

### "我的缓存不工作"

```
缓存问题？
├─ 任务运行但输出未恢复 → 缺少 `outputs` 键
├─ 意外的缓存未命中 → references/caching/gotchas.md
├─ 需要调试哈希输入 → 使用 --summarize 或 --dry
├─ 想要完全跳过缓存 → 使用 --force 或 cache: false
├─ 远程缓存不工作 → references/caching/remote-cache.md
└─ 环境导致未命中 → references/environment/gotchas.md
```

### "我只想运行更改的包"

```
只运行更改的内容？
├─ 更改的包 + 依赖项（推荐）→ turbo run build --affected
├─ 自定义基础分支 → --affected --affected-base=origin/develop
├─ 手动 git 比较 → --filter=...[origin/main]
└─ 查看所有过滤选项 → references/filtering/RULE.md
```

**`--affected` 是只运行更改包的主要方式。** 它会自动与默认分支进行比较并包含依赖项。

### "我想过滤包"

```
过滤包？
├─ 仅更改的包 → --affected（见上文）
├─ 按包名 → --filter=web
├─ 按目录 → --filter=./apps/*
├─ 包 + 依赖项 → --filter=web...
├─ 包 + 依赖者 → --filter=...web
└─ 复杂组合 → references/filtering/patterns.md
```

### "环境变量不工作"

```
环境问题？
├─ 运行时变量不可用 → 严格模式过滤（默认）
├─ 缓存命中但环境错误 → 变量不在 `env` 键中
├─ .env 更改不导致重新构建 → .env 不在 `inputs` 中
├─ CI 变量缺失 → references/environment/gotchas.md
└─ 框架变量（NEXT_PUBLIC_*）→ 通过推断自动包含
```

### "我需要设置 CI"

```
CI 设置？
├─ GitHub Actions → references/ci/github-actions.md
├─ Vercel 部署 → references/ci/vercel.md
├─ CI 中的远程缓存 → references/caching/remote-cache.md
├─ 仅构建更改的包 → --affected 标志
├─ 跳过不必要的构建 → turbo-ignore（references/cli/commands.md）
└─ 无更改时跳过容器设置 → turbo-ignore
```

### "我想在开发期间监视更改"

```
监视模式？
├─ 更改时重新运行任务 → turbo watch（references/watch/RULE.md）
├─ 带依赖项的开发服务器 → 使用 `with` 键（references/configuration/tasks.md#with）
├─ 依赖项更改时重启开发服务器 → 使用 `interruptible: true`
└─ 持久开发任务 → 使用 `persistent: true`
```

### "我需要创建/结构化一个包"

```
包创建/结构？
├─ 创建内部包 → references/best-practices/packages.md
├─ 仓库结构 → references/best-practices/structure.md
├─ 依赖管理 → references/best-practices/dependencies.md
├─ 最佳实践概述 → references/best-practices/RULE.md
├─ JIT vs 编译包 → references/best-practices/packages.md#compilation-strategies
└─ 在应用之间共享代码 → references/best-practices/RULE.md#package-types
```

### "我应该如何构建我的 monorepo？"

```
Monorepo 结构？
├─ 标准布局（apps/、packages/）→ references/best-practices/RULE.md
├─ 包类型（应用 vs 库）→ references/best-practices/RULE.md#package-types
├─ 创建内部包 → references/best-practices/packages.md
├─ TypeScript 配置 → references/best-practices/structure.md#typescript-configuration
├─ ESLint 配置 → references/best-practices/structure.md#eslint-configuration
├─ 依赖管理 → references/best-practices/dependencies.md
└─ 强制包边界 → references/boundaries/RULE.md
```

### "我想强制执行架构边界"

```
强制边界？
├─ 检查违规 → turbo boundaries
├─ 标记包 → references/boundaries/RULE.md#tags
├─ 限制哪些包可以导入其他包 → references/boundaries/RULE.md#rule-types
└─ 防止跨包文件导入 → references/boundaries/RULE.md
```

## 关键反模式

### 在代码中使用 `turbo` 简写

**在 package.json 脚本和 CI 管道中推荐使用 `turbo run`。** 简写 `turbo <task>` 仅用于交互式终端使用。

```json
// 错误 - 在 package.json 中使用简写
{
  "scripts": {
    "build": "turbo build",
    "dev": "turbo dev"
  }
}

// 正确
{
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev"
  }
}
```

```yaml
# 错误 - 在 CI 中使用简写
- run: turbo build --affected

# 正确
- run: turbo run build --affected
```

### 绕过 Turbo 的根脚本

根 `package.json` 脚本必须委派给 `turbo run`，而不是直接运行任务。

```json
// 错误 - 完全绕过 turbo
{
  "scripts": {
    "build": "bun build",
    "dev": "bun dev"
  }
}

// 正确 - 委派给 turbo
{
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev"
  }
}
```

### 使用 `&&` 链接 Turbo 任务

不要用 `&&` 链接 turbo 任务。让 turbo 来编排。

```json
// 错误 - turbo 任务未使用 turbo run
{
  "scripts": {
    "changeset:publish": "bun build && changeset publish"
  }
}

// 正确
{
  "scripts": {
    "changeset:publish": "turbo run build && changeset publish"
  }
}
```

### 手动构建依赖项的 `prebuild` 脚本

像 `prebuild` 这样手动构建其他包的脚本会绕过 Turborepo 的依赖图。

```json
// 错误 - 手动构建依赖项
{
  "scripts": {
    "prebuild": "cd ../../packages/types && bun run build && cd ../utils && bun run build",
    "build": "next build"
  }
}
```

**但是，修复方法取决于是否声明了工作区依赖项：**

1. **如果已声明依赖项**（例如 package.json 中的 `"@repo/types": "workspace:*"`），删除 `prebuild` 脚本。Turbo 的 `dependsOn: ["^build"]` 会自动处理此问题。

2. **如果未声明依赖项**，`prebuild` 存在是因为没有依赖关系时 `^build` 不会触发。修复方法是：
   - 将依赖项添加到 package.json：`"@repo/types": "workspace:*"`
   - 然后删除 `prebuild` 脚本

```json
// 正确 - 声明依赖项，让 turbo 处理构建顺序
// package.json
{
  "dependencies": {
    "@repo/types": "workspace:*",
    "@repo/utils": "workspace:*"
  },
  "scripts": {
    "build": "next build"
  }
}

// turbo.json
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"]
    }
  }
}
```

**关键见解：** `^build` 仅在列为依赖项的包中运行构建。没有依赖项声明 = 没有自动构建顺序。

### 过于宽泛的 `globalDependencies`

`globalDependencies` 影响所有包中的所有任务。要具体。

```json
// 错误 - 重锤，影响所有哈希
{
  "globalDependencies": ["**/.env.*local"]
}

// 更好 - 移动到任务级别的 inputs
{
  "globalDependencies": [".env"],
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", ".env*"],
      "outputs": ["dist/**"]
    }
  }
}
```

### 重复的任务配置

查找可以合并的跨任务重复配置。Turborepo 支持共享配置模式。

```json
// 错误 - 跨任务重复的 env 和 inputs
{
  "tasks": {
    "build": {
      "env": ["API_URL", "DATABASE_URL"],
      "inputs": ["$TURBO_DEFAULT$", ".env*"]
    },
    "test": {
      "env": ["API_URL", "DATABASE_URL"],
      "inputs": ["$TURBO_DEFAULT$", ".env*"]
    },
    "dev": {
      "env": ["API_URL", "DATABASE_URL"],
      "inputs": ["$TURBO_DEFAULT$", ".env*"],
      "cache": false,
      "persistent": true
    }
  }
}

// 更好 - 使用 globalEnv 和 globalDependencies 进行共享配置
{
  "globalEnv": ["API_URL", "DATABASE_URL"],
  "globalDependencies": [".env*"],
  "tasks": {
    "build": {},
    "test": {},
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

**何时使用全局 vs 任务级别：**

- `globalEnv` / `globalDependencies` - 影响所有任务，用于真正的共享配置
- 任务级别的 `env` / `inputs` - 仅当特定任务需要时使用

### 不是反模式：大型 `env` 数组

大型 `env` 数组（甚至 50+ 变量）**不是**问题。这通常意味着用户在声明其构建的环境依赖项方面很彻底。不要将此标记为问题。

### 使用 `--parallel` 标志

`--parallel` 标志会绕过 Turborepo 的依赖图。如果任务需要并行执行，请正确配置 `dependsOn`。

```bash
# 错误 - 绕过依赖图
turbo run lint --parallel

# 正确 - 配置任务以允许并行执行
# 在 turbo.json 中，适当设置 dependsOn（或使用 transit nodes）
turbo run lint
```

### 根 turbo.json 中的包特定任务覆盖

当多个包需要不同的任务配置时，使用**包配置**（每个包中的 `turbo.json`），而不是用 `package#task` 覆盖使根 `turbo.json` 变得混乱。

```json
// 错误 - 根 turbo.json 中有许多包特定覆盖
{
  "tasks": {
    "test": { "dependsOn": ["build"] },
    "@repo/web#test": { "outputs": ["coverage/**"] },
    "@repo/api#test": { "outputs": ["coverage/**"] },
    "@repo/utils#test": { "outputs": [] },
    "@repo/cli#test": { "outputs": [] },
    "@repo/core#test": { "outputs": [] }
  }
}

// 正确 - 使用包配置
// 根 turbo.json - 仅基础配置
{
  "tasks": {
    "test": { "dependsOn": ["build"] }
  }
}

// packages/web/turbo.json - 包特定覆盖
{
  "extends": ["//"],
  "tasks": {
    "test": { "outputs": ["coverage/**"] }
  }
}

// packages/api/turbo.json
{
  "extends": ["//"],
  "tasks": {
    "test": { "outputs": ["coverage/**"] }
  }
}
```

**包配置的好处：**

- 保持配置靠近其影响的代码
- 根 turbo.json 保持清洁并专注于基本模式
- 更容易理解每个包的特殊之处
- 与 `$TURBO_EXTENDS$` 配合使用以继承和扩展数组

**何时在根中使用 `package#task`：**

- 单个包需要唯一的依赖项（例如 `"deploy": { "dependsOn": ["web#build"] }`）
- 迁移时的临时覆盖

详见 `references/configuration/RULE.md#package-configurations`。

### 在 `inputs` 中使用 `../` 跳出包

不要使用像 `../` 这样的相对路径来引用包外的文件。使用 `$TURBO_ROOT$` 代替。

```json
// 错误 - 跳出包
{
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", "../shared-config.json"]
    }
  }
}

// 正确 - 使用 $TURBO_ROOT$ 作为仓库根目录
{
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", "$TURBO_ROOT$/shared-config.json"]
    }
  }
}
```
