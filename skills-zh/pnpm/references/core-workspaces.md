---
name: pnpm-workspaces
description: 通过工作区管理多个包的 monorepo 支持
---

# pnpm 工作区

pnpm 通过工作区内置支持 monorepo（多包仓库）。

## 设置工作区

在仓库根目录创建 `pnpm-workspace.yaml`：

```yaml
packages:
  # 包含 packages/ 目录中的所有包
  - 'packages/*'
  # 包含所有应用
  - 'apps/*'
  # 包含嵌套包
  - 'tools/*/packages/*'
  # 排除测试目录
  - '!**/test/**'
```

## 工作区协议

使用 `workspace:` 协议引用本地包：

```json
{
  "dependencies": {
    "@myorg/utils": "workspace:*",
    "@myorg/core": "workspace:^",
    "@myorg/types": "workspace:~"
  }
}
```

### 协议变体

| 协议 | 行为 | 发布为 |
|----------|----------|--------------|
| `workspace:*` | 任何版本 | 实际版本（例如 `1.2.3`） |
| `workspace:^` | 兼容版本 | `^1.2.3` |
| `workspace:~` | 补丁版本 | `~1.2.3` |
| `workspace:^1.0.0` | Semver 范围 | `^1.0.0` |

## 过滤包

使用 `--filter` 在特定包上运行命令：

```bash
# 按包名
pnpm --filter @myorg/app build
pnpm -F @myorg/app build

# 按目录路径
pnpm --filter "./packages/core" test

# Glob 模式
pnpm --filter "@myorg/*" lint
pnpm --filter "!@myorg/internal-*" publish

# 所有包
pnpm -r build
pnpm --recursive build
```

### 基于依赖的过滤

```bash
# 包及其所有依赖项
pnpm --filter "...@myorg/app" build

# 包及其所有依赖者
pnpm --filter "@myorg/core..." test

# 两个方向
pnpm --filter "...@myorg/shared..." build

# 自 git 引用以来更改的包
pnpm --filter "...[origin/main]" test
pnpm --filter "[HEAD~5]" lint
```

## 工作区命令

### 安装依赖
```bash
# 安装所有工作区包
pnpm install

# 向特定包添加依赖
pnpm --filter @myorg/app add lodash

# 添加工作区依赖
pnpm --filter @myorg/app add @myorg/utils
```

### 运行脚本
```bash
# 在所有具有该脚本的包中运行
pnpm -r run build

# 按拓扑顺序运行（依赖项优先）
pnpm -r --workspace-concurrency=1 run build

# 并行运行
pnpm -r --parallel run test

# 流式输出
pnpm -r --stream run dev
```

### 执行命令
```bash
# 在所有包中运行命令
pnpm -r exec pwd

# 在特定包中运行
pnpm --filter "./packages/**" exec rm -rf dist
```

## 工作区设置

在 `.npmrc` 或 `pnpm-workspace.yaml` 中配置：

```ini
# 自动链接工作区包
link-workspace-packages=true

# 优先使用工作区包而非注册表
prefer-workspace-packages=true

# 单个锁文件（推荐）
shared-workspace-lockfile=true

# 工作区协议处理
save-workspace-protocol=rolling

# 并发工作区脚本
workspace-concurrency=4
```

## 发布工作区

发布时，`workspace:` 协议被转换：

```json
// 发布前
{
  "dependencies": {
    "@myorg/utils": "workspace:^"
  }
}

// 发布后
{
  "dependencies": {
    "@myorg/utils": "^1.2.3"
  }
}
```

使用 `--no-git-checks` 从 CI 发布：
```bash
pnpm publish -r --no-git-checks
```

## 最佳实践

1. **使用工作区协议** 用于内部依赖
2. **启用 `link-workspace-packages`** 用于自动链接
3. **使用共享锁文件** 以保持一致性
4. **按依赖项过滤** 在构建时确保正确顺序
5. **使用目录** 用于共享的外部依赖版本

## 示例项目结构

```
my-monorepo/
├── pnpm-workspace.yaml
├── package.json
├── pnpm-lock.yaml
├── packages/
│   ├── core/
│   │   └── package.json
│   ├── utils/
│   │   └── package.json
│   └── types/
│       └── package.json
└── apps/
    ├── web/
    │   └── package.json
    └── api/
        └── package.json
```

<!--
源引用:
- https://pnpm.io/workspaces
- https://pnpm.io/filtering
- https://pnpm.io/npmrc#workspace-settings
-->
