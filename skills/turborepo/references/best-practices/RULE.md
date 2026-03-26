# Monorepo 最佳实践

构建和维护健康 Turborepo monorepo 的基本模式。

## 仓库结构

### 标准布局

```
my-monorepo/
├── apps/                    # 应用包（可部署）
│   ├── web/
│   ├── docs/
│   └── api/
├── packages/                # 库包（共享代码）
│   ├── ui/
│   ├── utils/
│   └── config-*/           # 共享配置（eslint、typescript 等）
├── package.json            # 根 package.json（最少依赖）
├── turbo.json              # Turborepo 配置
├── pnpm-workspace.yaml     # (pnpm) 或 package.json 中的 workspaces
└── pnpm-lock.yaml          # 锁文件（必需）
```

### 关键原则

1. **`apps/` 用于可部署项**：Next.js 站点、API、CLI - 被部署的东西
2. **`packages/` 用于库**：被应用或其他包使用的共享代码
3. **每个包一个目的**：每个包应该做好一件事
4. **没有嵌套包**：不要把包放在包里面

## 包类型

### 应用包（`apps/`）

- **可部署**：这些是包图的"端点"
- **不被其他包安装**：应用不应该是其他包的依赖项
- **没有共享代码**：如果代码需要共享，提取到 `packages/`

```json
// apps/web/package.json
{
  "name": "web",
  "private": true,
  "dependencies": {
    "@repo/ui": "workspace:*",
    "next": "latest"
  }
}
```

### 库包（`packages/`）

- **共享代码**：工具、组件、配置
- **命名空间名称**：使用 `@repo/` 或 `@yourorg/` 前缀
- **清晰的导出**：定义包暴露什么

```json
// packages/ui/package.json
{
  "name": "@repo/ui",
  "exports": {
    "./button": "./src/button.tsx",
    "./card": "./src/card.tsx"
  }
}
```

## 包编译策略

### 即时编译（最简单）

直接导出 TypeScript；让应用的打包器编译它。

```json
{
  "name": "@repo/ui",
  "exports": {
    "./button": "./src/button.tsx"
  }
}
```

**优点**：零构建配置，即时更改
**缺点**：无法缓存构建，需要应用打包器支持

### 编译（推荐用于库）

包使用 `tsc` 或打包器自行编译。

```json
{
  "name": "@repo/ui",
  "exports": {
    "./button": {
      "types": "./src/button.tsx",
      "default": "./dist/button.js"
    }
  },
  "scripts": {
    "build": "tsc"
  }
}
```

**优点**：可被 Turborepo 缓存，随处可用
**缺点**：更多配置

## 依赖管理

### 在使用处安装

在使用依赖的包中安装依赖，而不是在根目录。

```bash
# 好：在需要的包中安装
pnpm add lodash --filter=@repo/utils

# 避免：在根目录安装所有内容
pnpm add lodash -w  # 仅用于仓库级工具
```

### 根依赖

只有这些属于根 `package.json`：

- `turbo` - 构建系统
- `husky`、`lint-staged` - Git 钩子
- 仓库级工具

### 内部依赖

对内部包使用工作区协议：

```json
// pnpm/bun
{ "@repo/ui": "workspace:*" }

// npm/yarn
{ "@repo/ui": "*" }
```

## 导出最佳实践

### 使用 `exports` 字段（不是 `main`）

```json
{
  "exports": {
    ".": "./src/index.ts",
    "./button": "./src/button.tsx",
    "./utils": "./src/utils.ts"
  }
}
```

### 避免桶文件

不要创建重新导出所有内容的 `index.ts` 文件：

```typescript
// 错误：packages/ui/src/index.ts
export * from './button';
export * from './card';
export * from './modal';
// ... 即使您只需要一个东西，也会导入所有内容

// 好：package.json 中的直接导出
{
  "exports": {
    "./button": "./src/button.tsx",
    "./card": "./src/card.tsx"
  }
}
```

### 为包命名空间

```json
// 好
{ "name": "@repo/ui" }
{ "name": "@acme/utils" }

// 避免（与 npm 注册表冲突）
{ "name": "ui" }
{ "name": "utils" }
```

## 常见反模式

### 跨包边界访问文件

```typescript
// 错误：访问另一个包
import { Button } from "../../packages/ui/src/button";

// 好：正确安装和导入
import { Button } from "@repo/ui/button";
```

### 应用中的共享代码

```
// 错误
apps/
  web/
    shared/        # 这应该是一个包！
      utils.ts

// 好
packages/
  utils/           # 正确的共享包
    src/utils.ts
```

### 根依赖过多

```json
// 错误：根有应用依赖
{
  "dependencies": {
    "react": "^18",
    "next": "^14",
    "lodash": "^4"
  }
}

// 好：根只有仓库工具
{
  "devDependencies": {
    "turbo": "latest",
    "husky": "latest"
  }
}
```

## 另请参阅

- [structure.md](./structure.md) - 详细的仓库结构模式
- [packages.md](./packages.md) - 创建和管理内部包
- [dependencies.md](./dependencies.md) - 依赖管理策略
