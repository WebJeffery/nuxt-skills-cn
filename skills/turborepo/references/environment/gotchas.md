# 环境变量陷阱

常见错误以及如何修复它们。

## .env 文件必须在 `inputs` 中

Turbo 不读取 `.env` 文件。你的框架（Next.js、Vite 等）或 `dotenv` 加载它们。但 Turbo 需要知道它们何时更改。

**错误：**

```json
{
  "tasks": {
    "build": {
      "env": ["DATABASE_URL"]
    }
  }
}
```

**正确：**

```json
{
  "tasks": {
    "build": {
      "env": ["DATABASE_URL"],
      "inputs": ["$TURBO_DEFAULT$", ".env", ".env.local", ".env.production"]
    }
  }
}
```

## 严格模式过滤 CI 变量

在严格模式下，CI 提供商变量（GITHUB_TOKEN、GITLAB_CI 等）会被过滤，除非明确列出。

**症状：** 任务在 CI 中失败，显示"需要身份验证"或"权限被拒绝"。

**解决方案：**

```json
{
  "globalPassThroughEnv": ["GITHUB_TOKEN", "GITLAB_CI", "CI"]
}
```

## passThroughEnv 不影响哈希

`passThroughEnv` 中的变量在运行时可用，但更改不会触发重新构建。

**危险示例：**

```json
{
  "tasks": {
    "build": {
      "passThroughEnv": ["API_URL"]
    }
  }
}
```

如果 `API_URL` 从 staging 更改为 production，Turbo 可能会提供指向错误 API 的缓存构建。

**仅将 passThroughEnv 用于：**

- 不影响输出的身份验证令牌（SENTRY_AUTH_TOKEN）
- CI 元数据（GITHUB_RUN_ID）
- 构建后消耗的变量（部署凭据）

## 运行时创建的变量是不可见的

Turbo 在启动时捕获环境变量。执行期间创建的变量不会被看到。

**不起作用：**

```bash
# 在 package.json 脚本中
"build": "export API_URL=$COMPUTED_VALUE && next build"
```

**解决方案：** 在调用 turbo 之前设置变量：

```bash
API_URL=$COMPUTED_VALUE turbo run build
```

## 不同环境的不同 .env 文件

如果你使用 `.env.development` 和 `.env.production`，两者都应该在 inputs 中。

```json
{
  "tasks": {
    "build": {
      "inputs": [
        "$TURBO_DEFAULT$",
        ".env",
        ".env.local",
        ".env.development",
        ".env.development.local",
        ".env.production",
        ".env.production.local"
      ]
    }
  }
}
```

## 完整的 Next.js 示例

```json
{
  "$schema": "https://v2-8-18-canary-7.turborepo.dev/schema.json",
  "globalEnv": ["CI", "NODE_ENV", "VERCEL"],
  "globalPassThroughEnv": ["GITHUB_TOKEN", "VERCEL_URL"],
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "env": ["DATABASE_URL", "NEXT_PUBLIC_*", "!NEXT_PUBLIC_ANALYTICS_ID"],
      "passThroughEnv": ["SENTRY_AUTH_TOKEN"],
      "inputs": [
        "$TURBO_DEFAULT$",
        ".env",
        ".env.local",
        ".env.production",
        ".env.production.local"
      ],
      "outputs": [".next/**", "!.next/cache/**"]
    }
  }
}
```

此配置：

- 哈希 DATABASE*URL 和 NEXT_PUBLIC*\* 变量（分析除外）
- 在不哈希的情况下传递 SENTRY_AUTH_TOKEN
- 在哈希中包含所有 .env 文件变体
- 使 CI 令牌全局可用