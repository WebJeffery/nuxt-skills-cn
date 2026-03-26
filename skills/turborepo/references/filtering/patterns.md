# 常见过滤模式

典型 monorepo 场景的实用示例。

## 单个包

在一个包中运行任务：

```bash
turbo run build --filter=web
turbo run test --filter=@acme/api
```

## 包及其依赖项

构建包及其所有依赖项：

```bash
turbo run build --filter=web...
```

适用于：确保在目标之前构建所有依赖项。

## 包的依赖项

在依赖于库的所有包中运行：

```bash
turbo run test --filter=...ui
```

适用于：在更改共享包后测试使用者。

## 仅依赖项（排除目标）

测试依赖于 ui 的包，但不包括 ui 本身：

```bash
turbo run test --filter=...^ui
```

## 更改的包

仅在自上次提交以来有文件更改的包中运行：

```bash
turbo run lint --filter=[HEAD^1]
```

自特定分支点以来：

```bash
turbo run lint --filter=[main...HEAD]
```

## 更改 + 依赖项（PR 构建）

在更改的包及其依赖它们的包中运行：

```bash
turbo run build test --filter=...[HEAD^1]
```

或使用快捷方式：

```bash
turbo run build test --affected
```

## 基于目录

在所有应用中运行：

```bash
turbo run build --filter=./apps/*
```

在特定目录中运行：

```bash
turbo run build --filter=./apps/web --filter=./apps/api
```

## 基于作用域

在作用域下的所有包中运行：

```bash
turbo run build --filter=@acme/*
```

## 排除

在所有应用中运行，除了 admin：

```bash
turbo run build --filter=./apps/* --filter=!admin
```

在任何地方运行，除了特定包：

```bash
turbo run lint --filter=!legacy-app --filter=!deprecated-pkg
```

## 复杂组合

更改的应用及其依赖项：

```bash
turbo run build --filter=...[HEAD^1] --filter=./apps/*
```

除 docs 外的所有包，但仅当更改时：

```bash
turbo run build --filter=[main...HEAD] --filter=!docs
```

## 调试过滤器

使用 `--dry` 查看将要运行的内容而不执行：

```bash
turbo run build --filter=web... --dry
```

使用 `--dry=json` 获取机器可读的输出：

```bash
turbo run build --filter=...[HEAD^1] --dry=json
```

## CI/CD 模式

PR 验证（最常见）：

```bash
turbo run build test lint --affected
```

仅部署更改的应用：

```bash
turbo run deploy --filter=./apps/* --filter=[main...HEAD]
```

特定应用及其依赖项的完整重建：

```bash
turbo run build --filter=production-app...
```