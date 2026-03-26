# 配置陷阱

常见错误以及如何修复它们。

## #1 根脚本未使用 `turbo run`

根 `package.json` 中用于 turbo 任务的脚本必须使用 `turbo run`，而不是直接命令。

```json
// 错误 - 绕过 turbo，没有并行化或缓存
{
  "scripts": {
    "build": "bun build",
    "dev": "bun dev"
  }
}

// 正确 - 委托给 turbo
{
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev"
  }
}
```

**为什么这很重要：** 在根目录运行 `bun build` 或 `npm run build` 会完全绕过 Turborepo - 没有并行化，没有缓存，没有依赖图感知。

## #2 使用 `&&` 链接 Turbo 任务

不要使用 `&&` 来链接 turbo 应该编排的任务。

```json
// 错误 - changeset:publish 将 turbo 任务与非 turbo 命令链接
{
  "scripts": {
    "changeset:publish": "bun build && changeset publish"
  }
}

// 正确 - 使用 turbo run，让 turbo 处理依赖
{
  "scripts": {
    "changeset:publish": "turbo run build && changeset publish"
  }
}
```

如果第二个命令（`changeset publish`）依赖于构建输出，turbo 任务应该通过 turbo 运行以获得缓存和并行化优势。

## #3 过于宽泛的 globalDependencies

`globalDependencies` 影响所有包中所有任务的哈希。要具体。

```json
// 错误 - 影响所有哈希
{
  "globalDependencies": ["**/.env.*local"]
}

// 正确 - 移动到需要它的特定任务
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

**为什么这很重要：** `**/.env.*local` 匹配所有包中的 .env 文件，导致不必要的缓存失效。相反：

- 仅对真正全局的文件（根 `.env`）使用 `globalDependencies`
- 对于特定于包的 .env 文件，使用任务级别的 `inputs` 和 `$TURBO_DEFAULT$` 来保留默认行为

## #4 重复的任务配置

查找可以折叠的任务之间的重复配置。

```json
// 错误 - 任务之间重复的 env 和 inputs
{
  "tasks": {
    "build": {
      "env": ["API_URL", "DATABASE_URL"],
      "inputs": ["$TURBO_DEFAULT$", ".env*"]
    },
    "test": {
      "env": ["API_URL", "DATABASE_URL"],
      "inputs": ["$TURBO_DEFAULT$", ".env*"]
    }
  }
}

// 更好 - 使用 globalEnv 和 globalDependencies
{
  "globalEnv": ["API_URL", "DATABASE_URL"],
  "globalDependencies": [".env*"],
  "tasks": {
    "build": {},
    "test": {}
  }
}
```

**何时使用全局与任务级别：**

- `globalEnv` / `globalDependencies` - 影响所有任务，用于真正共享的配置
- 任务级别的 `env` / `inputs` - 仅当特定任务需要时使用

## #5 在 `inputs` 中使用 `../` 遍历包外

不要使用像 `../` 这样的相对路径来引用包外的文件。使用 `$TURBO_ROOT$` 代替。

```json
// 错误 - 遍历出包
{
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", "../shared-config.json"]
    }
  }
}

// 正确 - 使用 $TURBO_ROOT$ 引用仓库根
{
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", "$TURBO_ROOT$/shared-config.json"]
    }
  }
}
```

## #6 最常见的错误：创建根任务

**不要创建根任务。始终创建包任务。**

当你需要创建任务（build、lint、test、typecheck 等）时：

1. 将脚本添加到**每个相关包的** `package.json`
2. 在根 `turbo.json` 中注册任务
3. 根 `package.json` 仅包含 `turbo run <task>`

```json
// 错误 - 不要这样做
// 根 package.json 带有任务逻辑
{
  "scripts": {
    "build": "cd apps/web && next build && cd ../api && tsc",
    "lint": "eslint apps/ packages/",
    "test": "vitest"
  }
}

// 正确 - 这样做
// apps/web/package.json
{ "scripts": { "build": "next build", "lint": "eslint .", "test": "vitest" } }

// apps/api/package.json
{ "scripts": { "build": "tsc", "lint": "eslint .", "test": "vitest" } }

// packages/ui/package.json
{ "scripts": { "build": "tsc", "lint": "eslint .", "test": "vitest" } }

// 根 package.json - 仅委托
{ "scripts": { "build": "turbo run build", "lint": "turbo run lint", "test": "turbo run test" } }

// turbo.json - 注册任务
{
  "tasks": {
    "build": { "dependsOn": ["^build"], "outputs": ["dist/**"] },
    "lint": {},
    "test": {}
  }
}
```

**为什么这很重要：**

- 包任务在所有包中**并行**运行
- 每个包的输出被**单独**缓存
- 你可以**过滤**到特定包：`turbo run test --filter=web`

根任务（`//#taskname`）会破坏所有这些优势。仅将它们用于真正无法在任何包中存在的任务（极其罕见）。

## #7 需要并行执行 + 缓存失效的任务

某些任务可以并行运行（不需要依赖的构建输出）但当依赖源代码更改时仍必须使缓存失效。使用 `dependsOn: ["^taskname"]` 强制顺序执行。使用无依赖会破坏缓存失效。

**对这些任务使用传递节点：**

```json
// 错误 - 强制顺序执行（慢）
"my-task": {
  "dependsOn": ["^my-task"]
}

// 也错误 - 没有依赖感知（不正确的缓存）
"my-task": {}

// 正确 - 使用传递节点进行并行 + 正确的缓存
{
  "tasks": {
    "transit": { "dependsOn": ["^transit"] },
    "my-task": { "dependsOn": ["transit"] }
  }
}
```

**为什么传递节点有效：**

- `transit` 创建依赖关系而不匹配任何实际脚本
- 依赖于 `transit` 的任务获得依赖感知
- 由于 `transit` 立即完成（没有脚本），任务并行运行
- 当依赖源代码更改时，缓存正确失效

**如何识别需要此模式的任务：** 查找从依赖项读取源文件但不需要其构建输出的任务。

## 生成文件的任务缺少输出

**在标记缺少 `outputs` 之前，检查任务实际产生什么：**

1. 读取包的脚本（例如，`"build": "tsc"`、`"test": "vitest"`）
2. 确定它是否写入磁盘或仅输出到 stdout
3. 仅当任务产生应该缓存的文件时才标记

```json
// 错误 - 构建产生文件但它们未被缓存
"build": {
  "dependsOn": ["^build"]
}

// 正确 - 输出被缓存
"build": {
  "dependsOn": ["^build"],
  "outputs": ["dist/**"]
}
```

对于仅 stdout 的任务，没有 `outputs` 键是可以的。对于生成文件的任务，缺少 `outputs` 意味着 Turbo 没有任何东西可以缓存。

## 在 dependsOn 中忘记 ^

```json
// 错误 - 在同一包中查找 "build"（无限循环或缺失）
"build": {
  "dependsOn": ["build"]
}

// 正确 - 首先运行依赖的 build
"build": {
  "dependsOn": ["^build"]
}
```

`^` 意味着"在依赖包中"，而不是"在此包中"。

## Dev 任务缺少 persistent

```json
// 错误 - 依赖任务无限期等待 dev "完成"
"dev": {
  "cache": false
}

// 正确
"dev": {
  "cache": false,
  "persistent": true
}
```

## 包配置缺少 extends

```json
// 错误 - packages/web/turbo.json
{
  "tasks": {
    "build": { "outputs": [".next/**"] }
  }
}

// 正确
{
  "extends": ["//"],
  "tasks": {
    "build": { "outputs": [".next/**"] }
  }
}
```

没有 `"extends": ["//"]`，包配置是无效的。

## 根任务需要特殊语法

要运行仅在根 `package.json` 中定义的任务：

```bash
# 错误
turbo run format

# 正确
turbo run //#format
```

在 dependsOn 中：

```json
"build": {
  "dependsOn": ["//#codegen"]  // 根包的 codegen
}
```

## 覆盖默认输入

```json
// 错误 - 仅监视测试文件，忽略源代码更改
"test": {
  "inputs": ["tests/**"]
}

// 正确 - 扩展默认值，添加测试文件
"test": {
  "inputs": ["$TURBO_DEFAULT$", "tests/**"]
}
```

没有 `$TURBO_DEFAULT$`，你替换所有默认文件监视。

## 缓存具有副作用的任务

```json
// 错误 - deploy 可能在缓存命中时被跳过
"deploy": {
  "dependsOn": ["build"]
}

// 正确
"deploy": {
  "dependsOn": ["build"],
  "cache": false
}
```

始终禁用 deploy、publish 或变更任务的缓存。