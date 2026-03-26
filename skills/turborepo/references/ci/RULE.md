# 使用 Turborepo 的 CI/CD

在持续集成环境中运行 Turborepo 的一般原则。

## 核心原则

### 在 CI 中始终使用 `turbo run`

**永远不要在 CI 或脚本中使用 `turbo <tasks>` 简写。** 始终使用 `turbo run`：

```bash
# 正确 - 始终在 CI、package.json、脚本中使用
turbo run build test lint

# 错误 - 简写仅用于一次性终端命令
turbo build test lint
```

简写 `turbo <tasks>` 仅用于人类或代理在终端中直接输入的一次性调用。在任何将命令写入代码的地方（CI、package.json、脚本），使用 `turbo run`。

### 启用远程缓存

远程缓存通过在运行之间共享缓存工件显著加快 CI 速度。

所需的环境变量：

```bash
TURBO_TOKEN=your_vercel_token
TURBO_TEAM=your_team_slug
```

### 在 PR 构建中使用 --affected

`--affected` 标志仅对自基础分支以来更改的包运行任务：

```bash
turbo run build test --affected
```

这需要 Git 历史记录来计算更改了什么。

## Git 历史记录要求

### 获取深度

`--affected` 需要访问合并基础。浅克隆会破坏这一点。

```yaml
# GitHub Actions
- uses: actions/checkout@v4
  with:
    fetch-depth: 2 # --affected 的最小值
    # 如果合并基础很远，使用 0 获取完整历史
```

### 为什么浅克隆会破坏 --affected

Turborepo 将当前 HEAD 与 `main` 的合并基础进行比较。如果未获取该提交，`--affected` 将回退到运行所有内容。

对于有许多提交的 PR，请考虑：

```yaml
fetch-depth: 0 # 完整历史
```

## 环境变量参考

| 变量            | 目的                              |
| ------------------- | ------------------------------------ |
| `TURBO_TOKEN`       | 用于远程缓存的 Vercel 访问令牌 |
| `TURBO_TEAM`        | 你的 Vercel 团队 slug                |
| `TURBO_REMOTE_ONLY` | 跳过本地缓存，仅使用远程    |
| `TURBO_LOG_ORDER`   | 设置为 `grouped` 以获得更清晰的 CI 日志 |

## 另请参阅

- [github-actions.md](./github-actions.md) - GitHub Actions 设置
- [vercel.md](./vercel.md) - Vercel 部署
- [patterns.md](./patterns.md) - CI 优化模式