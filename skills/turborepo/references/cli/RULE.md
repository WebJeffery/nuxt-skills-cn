# turbo run

在 monorepo 中执行任务的主要命令。

## 基本用法

```bash
# 完整形式（在 CI、package.json、脚本中使用）
turbo run <tasks>

# 简写（仅用于一次性终端调用）
turbo <tasks>
```

## 何时使用 `turbo run` vs `turbo`

**当命令写入代码时，始终使用 `turbo run`：**

- `package.json` 脚本
- CI/CD 工作流（GitHub Actions 等）
- Shell 脚本
- 文档
- 任何静态/提交的配置

**仅将 `turbo`（简写）用于：**

- 在终端中直接输入的一次性命令
- 人类或代理的临时调用

```json
// package.json - 始终使用 "turbo run"
{
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev",
    "lint": "turbo run lint",
    "test": "turbo run test"
  }
}
```

```yaml
// CI 工作流 - 始终使用 "turbo run"
- run: turbo run build --affected
- run: turbo run test --affected
```

```bash
// 终端一次性 - 简写可以
turbo build --filter=web
```

## 运行任务

在运行之前，必须在 `turbo.json` 中定义任务。

```bash
# 单个任务
turbo build

# 多个任务
turbo run build lint test

# 查看可用任务（不带参数运行）
turbo run
```

## 向脚本传递参数

使用 `--` 将参数传递给底层包脚本：

```bash
turbo run build -- --sourcemap
turbo test -- --watch
turbo lint -- --fix
```

`--` 之后的所有内容直接传递给任务的脚本。

## 包选择

默认情况下，turbo 在所有包中运行任务。使用 `--filter` 缩小范围：

```bash
turbo build --filter=web
turbo test --filter=./apps/*
```

完整过滤语法请参阅 `filtering/`。

## 快速参考

| 目标                | 命令                    |
| ------------------- | -------------------------- |
| 构建所有内容    | `turbo build`              |
| 构建一个包   | `turbo build --filter=web` |
| 多个任务      | `turbo build lint test`    |
| 向脚本传递参数 | `turbo build -- --arg`     |
| 预览运行         | `turbo build --dry`        |
| 强制重新构建       | `turbo build --force`      |
