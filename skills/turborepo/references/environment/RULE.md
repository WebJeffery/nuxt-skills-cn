# Turborepo 中的环境变量

Turborepo 提供对哪些环境变量影响任务哈希和运行时可用性的细粒度控制。

## 配置键

### `env` - 特定于任务的变量

影响特定任务哈希的变量。当这些更改时，仅该任务重新构建。

```json
{
  "tasks": {
    "build": {
      "env": ["DATABASE_URL", "API_KEY"]
    }
  }
}
```

### `globalEnv` - 影响所有任务的变量

影响每个任务哈希的变量。当这些更改时，所有任务重新构建。

```json
{
  "globalEnv": ["CI", "NODE_ENV"]
}
```

### `passThroughEnv` - 仅运行时变量（不哈希）

在运行时可用但不包含在哈希中的变量。**谨慎使用** - 更改不会触发重新构建。

```json
{
  "tasks": {
    "deploy": {
      "passThroughEnv": ["AWS_ACCESS_KEY_ID", "AWS_SECRET_ACCESS_KEY"]
    }
  }
}
```

### `globalPassThroughEnv` - 全局运行时变量

与 `passThroughEnv` 相同，但用于所有任务。

```json
{
  "globalPassThroughEnv": ["GITHUB_TOKEN"]
}
```

## 通配符和否定

### 通配符

使用 `*` 匹配多个变量：

```json
{
  "env": ["MY_API_*", "FEATURE_FLAG_*"]
}
```

这匹配 `MY_API_URL`、`MY_API_KEY`、`FEATURE_FLAG_DARK_MODE` 等。

### 否定

排除变量（对框架推断有用）：

```json
{
  "env": ["!NEXT_PUBLIC_ANALYTICS_ID"]
}
```

## 完整示例

```json
{
  "$schema": "https://v2-8-18-canary-7.turborepo.dev/schema.json",
  "globalEnv": ["CI", "NODE_ENV"],
  "globalPassThroughEnv": ["GITHUB_TOKEN", "NPM_TOKEN"],
  "tasks": {
    "build": {
      "env": ["DATABASE_URL", "API_*"],
      "passThroughEnv": ["SENTRY_AUTH_TOKEN"]
    },
    "test": {
      "env": ["TEST_DATABASE_URL"]
    }
  }
}
```