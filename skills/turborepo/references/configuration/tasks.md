# 任务配置参考

完整文档：https://turborepo.dev/docs/reference/configuration#tasks

## dependsOn

控制任务执行顺序。

```json
{
  "tasks": {
    "build": {
      "dependsOn": [
        "^build", // 首先运行依赖的 build 任务
        "codegen", // 首先运行同一包的 codegen 任务
        "shared#build" // 首先运行特定包的 build 任务
      ]
    }
  }
}
```

| 语法     | 含义                              |
| ---------- | ------------------------------------ |
| `^task`    | 首先在所有依赖中运行 `task` |
| `task`     | 首先在同一包中运行 `task`     |
| `pkg#task` | 首先运行特定包的任务    |

`^` 前缀至关重要 - 没有它，你引用的是同一包。

### 并行任务的传递节点

对于像 `lint` 和 `check-types` 这样可以并行运行但需要依赖感知缓存的任务：

```json
{
  "tasks": {
    "transit": { "dependsOn": ["^transit"] },
    "lint": { "dependsOn": ["transit"] },
    "check-types": { "dependsOn": ["transit"] }
  }
}
```

**不要使用 `dependsOn: ["^lint"]`** - 这会强制顺序执行。
**不要使用 `dependsOn: []`** - 这会破坏缓存失效。

`transit` 任务创建依赖关系而不运行任何内容（没有匹配的脚本），因此任务以正确的缓存并行运行。

## outputs

要缓存的文件的 glob 模式。**如果省略，则不缓存任何内容。**

```json
{
  "tasks": {
    "build": {
      "outputs": ["dist/**", "build/**"]
    }
  }
}
```

**框架示例：**

```json
// Next.js
"outputs": [".next/**", "!.next/cache/**"]

// Vite
"outputs": ["dist/**"]

// TypeScript (tsc)
"outputs": ["dist/**", "*.tsbuildinfo"]

// 没有文件输出（lint、typecheck）
"outputs": []
```

使用 `!` 前缀从缓存中排除模式。

## inputs

计算任务哈希时考虑的文件。默认为包中所有跟踪的文件。

```json
{
  "tasks": {
    "test": {
      "inputs": ["src/**", "tests/**", "vitest.config.ts"]
    }
  }
}
```

**特殊值：**

| 值                 | 含义                                 |
| --------------------- | --------------------------------------- |
| `$TURBO_DEFAULT$`     | 包含默认输入，然后添加/删除 |
| `$TURBO_ROOT$/<path>` | 从仓库根目录引用文件          |

```json
{
  "tasks": {
    "build": {
      "inputs": [
        "$TURBO_DEFAULT$",
        "!README.md",
        "$TURBO_ROOT$/tsconfig.base.json"
      ]
    }
  }
}
```

## env

要包含在任务哈希中的环境变量。

```json
{
  "tasks": {
    "build": {
      "env": [
        "API_URL",
        "NEXT_PUBLIC_*", // 通配符匹配
        "!DEBUG" // 从哈希中排除
      ]
    }
  }
}
```

此处列出的变量影响缓存命中 - 更改值会使缓存失效。

## cache

启用/禁用任务的缓存。默认：`true`。

```json
{
  "tasks": {
    "dev": { "cache": false },
    "deploy": { "cache": false }
  }
}
```

禁用于：开发服务器、部署命令、具有副作用的任务。

## persistent

标记不退出的长时间运行的任务。默认：`false`。

```json
{
  "tasks": {
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

开发服务器必需 - 没有它，依赖任务将无限期等待。

## interactive

允许任务接收 stdin 输入。默认：`false`。

```json
{
  "tasks": {
    "login": {
      "cache": false,
      "interactive": true
    }
  }
}
```

## outputLogs

控制何时显示日志。选项：`full`、`hash-only`、`new-only`、`errors-only`、`none`。

```json
{
  "tasks": {
    "build": {
      "outputLogs": "new-only" // 仅在缓存未命中时显示日志
    }
  }
}
```

## with

与此任务一起运行任务。对于需要运行时依赖的长时间运行的任务。

```json
{
  "tasks": {
    "dev": {
      "with": ["api#dev"],
      "persistent": true,
      "cache": false
    }
  }
}
```

与 `dependsOn` 不同，`with` 并发运行任务（而不是顺序）。用于需要其他服务运行的开发服务器。

## interruptible

允许 `turbo watch` 在更改时重新启动任务。默认：`false`。

```json
{
  "tasks": {
    "dev": {
      "persistent": true,
      "interruptible": true,
      "cache": false
    }
  }
}
```

用于不会自动检测依赖更改的开发服务器。

## description

任务的可读描述。

```json
{
  "tasks": {
    "build": {
      "description": "编译应用程序以进行生产部署"
    }
  }
}
```

仅用于文档 - 不影响执行或缓存。

## passThroughEnv

在运行时可用但不包含在缓存哈希中的环境变量。

```json
{
  "tasks": {
    "build": {
      "passThroughEnv": ["AWS_SECRET_KEY", "GITHUB_TOKEN"]
    }
  }
}
```

**警告**：对这些变量的更改不会导致缓存未命中。如果更改应该使缓存失效，请使用 `env`。

## extends（仅包配置）

控制包配置中的任务继承。

```json
// packages/ui/turbo.json
{
  "extends": ["//"],
  "tasks": {
    "lint": {
      "extends": false // 从此包中排除
    }
  }
}
```

| 值            | 行为                                                       |
| ---------------- | -------------------------------------------------------------- |
| `true`（默认） | 从根 turbo.json 继承                                   |
| `false`          | 从包中排除任务，或在没有继承的情况下定义新任务 |