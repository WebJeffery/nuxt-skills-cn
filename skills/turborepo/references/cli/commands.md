# turbo run 标志参考

完整文档：https://turborepo.dev/docs/reference/run

## 包选择

### `--filter` / `-F`

选择要在其中运行任务的特定包。

```bash
turbo build --filter=web
turbo build -F=@repo/ui -F=@repo/utils
turbo test --filter=./apps/*
```

完整语法（glob、依赖项、git 范围）请参阅 `filtering/`。

### 任务标识符语法（v2.2.4+）

直接运行特定包任务：

```bash
turbo run web#build              # 构建 web 包
turbo run web#build docs#lint    # 多个特定任务
```

### `--affected`

仅自基础分支以来更改的包中运行。

```bash
turbo build --affected
turbo test --affected --filter=./apps/*  # 与 filter 结合
```

**工作原理：**

- 默认：比较 `main...HEAD`
- 在 GitHub Actions 中：自动检测 `GITHUB_BASE_REF`
- 覆盖基础：`TURBO_SCM_BASE=development turbo build --affected`
- 覆盖头部：`TURBO_SCM_HEAD=your-branch turbo build --affected`

**需要 git 历史记录** - 浅克隆可能会回退到运行所有任务。

## 执行控制

### `--dry` / `--dry=json`

预览将要运行的内容而不执行。

```bash
turbo build --dry          # 人类可读
turbo build --dry=json     # 机器可读
```

### `--force`

忽略所有缓存工件，重新运行所有内容。

```bash
turbo build --force
```

### `--concurrency`

限制并行任务执行。

```bash
turbo build --concurrency=4      # 最多 4 个任务
turbo build --concurrency=50%    # 50% 的 CPU 核心
```

### `--continue`

当一个任务失败时，继续运行其他任务。

```bash
turbo build test --continue
```

### `--only`

仅运行指定的任务，跳过其依赖项。

```bash
turbo build --only  # 跳过运行 dependsOn 任务
```

### `--parallel`（不推荐）

忽略任务图依赖项，同时运行所有任务。**避免使用此标志**——如果任务需要并行运行，请正确配置 `dependsOn`。使用 `--parallel` 会绕过 Turborepo 的依赖图，这可能导致竞态条件和错误的构建。

## 缓存控制

### `--cache`

细粒度缓存行为控制。

```bash
# 默认：读写本地和远程
turbo build --cache=local:rw,remote:rw

# 只读本地，无远程
turbo build --cache=local:r,remote:

# 禁用本地，只读远程
turbo build --cache=local:,remote:r

# 禁用所有缓存
turbo build --cache=local:,remote:
```

## 输出和调试

### `--graph`

生成任务图可视化。

```bash
turbo build --graph                # 在浏览器中打开
turbo build --graph=graph.svg      # SVG 文件
turbo build --graph=graph.png      # PNG 文件
turbo build --graph=graph.json     # JSON 数据
turbo build --graph=graph.mermaid  # Mermaid 图表
```

### `--summarize`

生成 JSON 运行摘要用于调试。

```bash
turbo build --summarize
# 创建 .turbo/runs/<run-id>.json
```

### `--output-logs`

控制日志输出详细程度。

```bash
turbo build --output-logs=full        # 所有日志（默认）
turbo build --output-logs=new-only    # 仅缓存未命中
turbo build --output-logs=errors-only # 仅失败
turbo build --output-logs=none        # 静默
```

### `--profile`

生成 Chrome 跟踪配置文件用于性能分析。

```bash
turbo build --profile=profile.json
# 打开 chrome://tracing 并加载文件
```

### `--verbosity` / `-v`

控制 turbo 自己的日志级别。

```bash
turbo build -v      # 详细
turbo build -vv     # 更详细
turbo build -vvv    # 最大详细程度
```

## 环境

### `--env-mode`

控制环境变量处理。

```bash
turbo build --env-mode=strict  # 仅声明的环境变量（默认）
turbo build --env-mode=loose   # 在哈希中包含所有环境变量
```

## UI

### `--ui`

选择输出界面。

```bash
turbo build --ui=tui     # 交互式终端 UI（TTY 中默认）
turbo build --ui=stream  # 流式日志（CI 中默认）
```

---

# turbo-ignore

完整文档：https://turborepo.dev/docs/reference/turbo-ignore

当没有相关更改时跳过 CI 工作。对于跳过容器设置很有用。

## 基本用法

```bash
# 检查当前包是否需要构建（使用自动包作用域）
npx turbo-ignore

# 检查特定包
npx turbo-ignore web

# 检查特定任务
npx turbo-ignore --task=test
```

## 退出代码

- `0`：未检测到更改 - 跳过 CI 工作
- `1`：检测到更改 - 继续执行 CI

## CI 集成示例

```yaml
# GitHub Actions
- name: 检查更改
  id: turbo-ignore
  run: npx turbo-ignore web
  continue-on-error: true

- name: 构建
  if: steps.turbo-ignore.outcome == 'failure'  # 检测到更改
  run: pnpm build
```

## 比较深度

默认：与父提交比较（`HEAD^1`）。

```bash
# 与特定提交比较
npx turbo-ignore --fallback=abc123

# 与分支比较
npx turbo-ignore --fallback=main
```

---

# 其他命令

## turbo boundaries

检查工作区违规（实验性）。

```bash
turbo boundaries
```

配置请参阅 `references/boundaries/`。

## turbo watch

在文件更改时重新运行任务。

```bash
turbo watch build test
```

详细信息请参阅 `references/watch/`。

## turbo prune

为 Docker 创建稀疏检出。

```bash
turbo prune web --docker
```

## turbo link / unlink

连接/断开远程缓存。

```bash
turbo link    # 连接到 Vercel 远程缓存
turbo unlink  # 断开连接
```

## turbo login / logout

使用远程缓存提供程序进行身份验证。

```bash
turbo login   # 身份验证
turbo logout  # 登出
```

## turbo generate

搭建新包。

```bash
turbo generate
```
