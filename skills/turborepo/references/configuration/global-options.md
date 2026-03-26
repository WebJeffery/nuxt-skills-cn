# 全局选项参考

影响所有任务的选项。完整文档：https://turborepo.dev/docs/reference/configuration

## globalEnv

影响所有任务哈希的环境变量。

```json
{
  "globalEnv": ["CI", "NODE_ENV", "VERCEL_*"]
}
```

用于更改时应使所有缓存失效的变量。

## globalDependencies

影响所有任务哈希的文件。

```json
{
  "globalDependencies": ["tsconfig.json", ".env", "pnpm-lock.yaml"]
}
```

默认包含 lockfile。在此添加共享配置。

## globalPassThroughEnv

可用于任务但不包含在哈希中的变量。

```json
{
  "globalPassThroughEnv": ["AWS_SECRET_KEY", "GITHUB_TOKEN"]
}
```

用于不应影响缓存键的凭据。

## cacheDir

自定义缓存位置。默认：`node_modules/.cache/turbo`。

```json
{
  "cacheDir": ".turbo/cache"
}
```

## daemon

**已弃用**：守护进程不再用于 `turbo run`，此选项将在 3.0 版本中删除。守护进程仍由 `turbo watch` 和 Turborepo LSP 使用。

## envMode

如何处理未指定的环境变量。默认：`"strict"`。

```json
{
  "envMode": "strict"  // 仅指定的变量可用
  // 或
  "envMode": "loose"   // 所有变量通过
}
```

严格模式捕获缺少的环境声明。

## ui

终端 UI 模式。默认：`"stream"`。

```json
{
  "ui": "tui"     // 交互式终端 UI
  // 或
  "ui": "stream"  // 传统流式日志
}
```

TUI 为并行任务提供更好的 UX。

## remoteCache

配置远程缓存。

```json
{
  "remoteCache": {
    "enabled": true,
    "signature": true,
    "timeout": 30,
    "uploadTimeout": 60
  }
}
```

| 选项          | 默认值                | 描述                                            |
| --------------- | ---------------------- | ------------------------------------------------------ |
| `enabled`       | `true`                 | 启用/禁用远程缓存                          |
| `signature`     | `false`                | 使用 `TURBO_REMOTE_CACHE_SIGNATURE_KEY` 签名工件 |
| `preflight`     | `false`                | 在缓存请求之前发送 OPTIONS 请求             |
| `timeout`       | `30`                   | 缓存操作的超时时间（秒）                |
| `uploadTimeout` | `60`                   | 上传的超时时间（秒）                         |
| `apiUrl`        | `"https://vercel.com"` | 远程缓存 API 端点                              |
| `loginUrl`      | `"https://vercel.com"` | 登录端点                                         |
| `teamId`        | -                      | 团队 ID（必须以 `team_` 开头）                      |
| `teamSlug`      | -                      | 用于查询字符串的团队 slug                              |

有关设置，请参阅 https://turborepo.dev/docs/core-concepts/remote-caching。

## concurrency

默认：`"10"`

限制并行任务执行。

```json
{
  "concurrency": "4"     // 最多 4 个任务
  // 或
  "concurrency": "50%"   // 50% 的可用 CPU
}
```

## futureFlags

启用将在未来版本中成为默认值的实验性功能。

```json
{
  "futureFlags": {
    "errorsOnlyShowHash": true
  }
}
```

### `errorsOnlyShowHash`

使用 `outputLogs: "errors-only"` 时，在开始/完成时显示任务哈希：

- 缓存未命中：`cache miss, executing <hash> (only logging errors)`
- 缓存命中：`cache hit, replaying logs (no errors) <hash>`

### `longerSignatureKey`

当 `remoteCache.signature` 启用时，为 `TURBO_REMOTE_CACHE_SIGNATURE_KEY` 强制执行 32 字节的最小密钥长度。短密钥会削弱 HMAC-SHA256 签名。如果密钥太短，立即失败运行。

## noUpdateNotifier

当有新的 turbo 版本可用时禁用更新通知。

```json
{
  "noUpdateNotifier": true
}
```

## dangerouslyDisablePackageManagerCheck

绕过 `packageManager` 字段要求。用于增量迁移。

```json
{
  "dangerouslyDisablePackageManagerCheck": true
}
```

**警告**：不稳定的 lockfile 可能导致不可预测的行为。

## Git Worktree 缓存共享

在 Git worktree 中工作时，Turborepo 自动在主 worktree 和链接的 worktree 之间共享本地缓存。

**工作原理：**

- 检测 worktree 配置
- 将缓存重定向到主 worktree 的 `.turbo/cache`
- 与远程缓存一起工作

**优势：**

- 跨分支缓存命中
- 减少磁盘使用
- 更快的分支切换

**禁用方式**：在 turbo.json 中设置显式 `cacheDir`。