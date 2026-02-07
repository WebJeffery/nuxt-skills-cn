---
name: pnpm-catalogs
description: 工作区的集中式依赖版本管理
---

# pnpm 目录

目录提供了一种在工作区中集中管理依赖版本的方法。定义一次，随处使用。

## 基本用法

在 `pnpm-workspace.yaml` 中定义目录：

```yaml
packages:
  - 'packages/*'

catalog:
  react: ^18.2.0
  react-dom: ^18.2.0
  typescript: ~5.3.0
  vite: ^5.0.0
```

在 `package.json` 中使用 `catalog:` 引用：

```json
{
  "dependencies": {
    "react": "catalog:",
    "react-dom": "catalog:"
  },
  "devDependencies": {
    "typescript": "catalog:",
    "vite": "catalog:"
  }
}
```

## 命名目录

为不同场景创建多个目录：

```yaml
packages:
  - 'packages/*'

# 默认目录
catalog:
  lodash: ^4.17.21

# 命名目录
catalogs:
  react17:
    react: ^17.0.2
    react-dom: ^17.0.2
  
  react18:
    react: ^18.2.0
    react-dom: ^18.2.0
  
  testing:
    vitest: ^1.0.0
    "@testing-library/react": ^14.0.0
```

引用命名目录：

```json
{
  "dependencies": {
    "react": "catalog:react18",
    "react-dom": "catalog:react18"
  },
  "devDependencies": {
    "vitest": "catalog:testing"
  }
}
```

## 优势

1. **单一事实来源**：在一处更新版本
2. **一致性**：所有包使用相同版本
3. **轻松升级**：更改一次版本，影响整个工作区
4. **类型安全**：pnpm-workspace.yaml 中的 TypeScript 支持

## 目录与覆盖

| 功能 | 目录 | 覆盖 |
|---------|----------|-----------|
| 目的 | 定义直接依赖的版本 | 强制任何依赖的版本 |
| 范围 | 仅直接依赖 | 所有依赖（包括传递依赖） |
| 用法 | `"pkg": "catalog:"` | 自动应用 |
| 选择加入 | 每个 package.json 显式 | 工作区全局 |

## 使用目录发布

发布时，`catalog:` 引用被替换为实际版本：

```json
// 发布前（源）
{
  "dependencies": {
    "react": "catalog:"
  }
}

// 发布后（已发布的包）
{
  "dependencies": {
    "react": "^18.2.0"
  }
}
```

## 从覆盖迁移

如果您使用覆盖进行版本一致性：

```yaml
# 之前（使用覆盖）
overrides:
  react: ^18.2.0
  react-dom: ^18.2.0
```

迁移到目录以进行更清晰的依赖管理：

```yaml
# 之后（使用目录）
catalog:
  react: ^18.2.0
  react-dom: ^18.2.0
```

然后更新 package.json 文件以使用 `catalog:`。

## 最佳实践

1. **使用默认目录** 用于常用共享依赖
2. **使用命名目录** 用于版本变体（例如，不同的 React 版本）
3. **保持目录最小化** - 仅包含共享依赖
4. **与工作区协议结合** 用于内部包

```yaml
catalog:
  # 外部共享依赖
  lodash: ^4.17.21
  zod: ^3.22.0

# 内部包使用 workspace: 协议代替
# "dependencies": { "@myorg/utils": "workspace:^" }
```

<!--
源引用:
- https://pnpm.io/catalogs
-->
