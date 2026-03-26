# Vercel 部署

Turborepo 与 Vercel 无缝集成，用于 monorepo 部署。

## 远程缓存

部署到 Vercel 时，远程缓存**自动启用**。无需配置 - Vercel 检测 Turborepo 并启用缓存。

这意味着：

- 在 Vercel 上不需要 `TURBO_TOKEN` 或 `TURBO_TEAM` 设置
- 缓存在所有部署之间共享
- 预览和生产构建受益于缓存

## turbo-ignore

当包未更改时，使用 `turbo-ignore` 跳过不必要的构建。

### 安装

```bash
npx turbo-ignore
```

或在项目中全局安装：

```bash
pnpm add -D turbo-ignore
```

### 在 Vercel 中设置

1. 转到 Vercel 仪表板中的项目
2. 导航到设置 > Git > 忽略构建步骤
3. 选择"自定义"并输入：

```bash
npx turbo-ignore
```

### 工作原理

`turbo-ignore` 检查当前包（或其依赖）自上次成功部署以来是否更改：

1. 将当前提交与上次部署的提交进行比较
2. 使用 Turborepo 的依赖图
3. 如果没有更改，返回退出代码 0（跳过）
4. 如果检测到更改，返回退出代码 1（构建）

### 选项

```bash
# 检查特定包
npx turbo-ignore web

# 使用特定的比较引用
npx turbo-ignore --fallback=HEAD~1

# 详细输出
npx turbo-ignore --verbose
```

## 环境变量

在 Vercel 仪表板中设置环境变量：

1. 转到项目设置 > 环境变量
2. 为每个环境（生产、预览、开发）添加变量

常见变量：

- `DATABASE_URL`
- `API_KEY`
- 特定于包的配置

## Monorepo 根目录

对于 monorepo，在 Vercel 中设置根目录：

1. 项目设置 > 常规 > 根目录
2. 设置为包路径（例如，`apps/web`）

Vercel 自动：

- 从 monorepo 根目录安装依赖
- 从包目录运行构建
- 检测框架设置

## 构建命令

当根目录存在 `turbo.json` 时，Vercel 自动检测 `turbo run build`。

如果需要，覆盖：

```bash
turbo run build --filter=web
```

或用于仅生产优化：

```bash
turbo run build --filter=web --env-mode=strict
```