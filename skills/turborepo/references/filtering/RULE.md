# Turborepo 过滤语法参考

## 仅运行更改的包：`--affected`

**仅运行更改的包的主要方式是 `--affected`：**

```bash
# 仅在更改的包及其依赖项中运行 build/test/lint
turbo run build test lint --affected
```

这会将你的当前分支与默认分支（通常是 `main` 或 `master`）进行比较，并在以下情况运行任务：

1. 具有文件更改的包
2. 依赖于更改的包的包（依赖项）

### 为什么要包含依赖项？

如果你更改 `@repo/ui`，导入 `@repo/ui` 的包（如 `apps/web`）需要重新运行其任务以验证它们在更改后仍然工作。

### 自定义 --affected

```bash
# 使用不同的基础分支
turbo run build --affected --affected-base=origin/develop

# 使用不同的头部（当前状态）
turbo run build --affected --affected-head=HEAD~5
```

### 常见 CI 模式

```yaml
# .github/workflows/ci.yml
- run: turbo run build test lint --affected
```

这是最高效的 CI 设置 - 仅对实际更改的内容运行任务。

---

## 使用 --filter 进行手动 Git 比较

对于更多控制，将 `--filter` 与 git 比较语法一起使用：

```bash
# 更改的包 + 依赖项（与 --affected 相同）
turbo run build --filter=...[origin/main]

# 仅更改的包（无依赖项）
turbo run build --filter=[origin/main]

# 更改的包 + 依赖项（它们导入的包）
turbo run build --filter=[origin/main]...

# 自上次提交以来的更改
turbo run build --filter=...[HEAD^1]

# 两次提交之间的更改
turbo run build --filter=[a1b2c3d...e4f5g6h]
```

### 比较语法

| 语法        | 含义                               |
| ------------- | ------------------------------------- |
| `[ref]`       | 自 `ref` 以来更改的包          |
| `...[ref]`    | 更改的包 + 其依赖项   |
| `[ref]...`    | 更改的包 + 其依赖项 |
| `...[ref]...` | 依赖项、更改的包及其依赖项  |

---

## 其他过滤类型

过滤选择在 `turbo run` 调用中包含哪些包。

### 基本语法

```bash
turbo run build --filter=<package-name>
turbo run build -F <package-name>
```

多个过滤器组合为并集（匹配任何过滤器的包都会运行）。

### 按包名

```bash
--filter=web          # 精确匹配
--filter=@acme/*      # 作用域 glob
--filter=*-app        # 名称 glob
```

### 按目录

```bash
--filter=./apps/*           # apps/ 中的所有包
--filter=./packages/ui      # 特定目录
```

### 按依赖/依赖项

| 语法      | 含义                                |
| ----------- | -------------------------------------- |
| `pkg...`    | 包及其所有依赖项       |
| `...pkg`    | 包及其所有依赖项         |
| `...pkg...` | 依赖项、包及其依赖项  |
| `^pkg...`   | 仅依赖项（排除包本身） |
| `...^pkg`   | 仅依赖项（排除包本身）   |

### 否定

使用 `!` 排除包：

```bash
--filter=!web              # 排除 web
--filter=./apps/* --filter=!admin   # apps 除了 admin
```

### 任务标识符

在特定包中运行特定任务：

```bash
turbo run web#build        # 仅 web 的 build 任务
turbo run web#build api#test   # web build + api test
```

### 组合过滤器

多个 `--filter` 标志创建并集：

```bash
turbo run build --filter=web --filter=api   # 在两者中运行
```

---

## 快速参考：更改的包

| 目标                               | 命令                                                     |
| ---------------------------------- | ----------------------------------------------------------- |
| 更改 + 依赖项（推荐） | `turbo run build --affected`                                |
| 自定义基础分支                 | `turbo run build --affected --affected-base=origin/develop` |
| 仅更改（无依赖项）       | `turbo run build --filter=[origin/main]`                    |
| 更改 + 依赖项             | `turbo run build --filter=[origin/main]...`                 |
| 自上次提交以来                  | `turbo run build --filter=...[HEAD^1]`                      |