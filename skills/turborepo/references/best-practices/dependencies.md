# 依赖管理

在 Turborepo monorepo 中管理依赖的最佳实践。

## 核心原则：在使用处安装

依赖属于使用它们的包，而不是根目录。

```bash
# 好：在特定包中安装
pnpm add react --filter=@repo/ui
pnpm add next --filter=web

# 避免：在根目录安装
pnpm add react -w  # 仅用于仓库级工具！
```

## 本地安装的好处

### 1. 清晰性

每个包的 `package.json` 准确列出它需要的内容：

```json
// packages/ui/package.json
{
  "dependencies": {
    "react": "^18.0.0",
    "class-variance-authority": "^0.7.0"
  }
}
```

### 2. 灵活性

不同包可以在需要时使用不同版本：

```json
// packages/legacy-ui/package.json
{ "dependencies": { "react": "^17.0.0" } }

// packages/ui/package.json
{ "dependencies": { "react": "^18.0.0" } }
```

### 3. 更好的缓存

在根目录安装会更改工作区锁文件，使所有缓存失效。

### 4. 修剪支持

`turbo prune` 可以为 Docker 镜像删除未使用的依赖项。

## 根目录中应该有什么

只有仓库级工具：

```json
// 根 package.json
{
  "devDependencies": {
    "turbo": "latest",
    "husky": "^8.0.0",
    "lint-staged": "^15.0.0"
  }
}
```

**不是**应用依赖：

- react、next、express
- lodash、axios、zod
- 测试库（除非真正是仓库范围的）

## 安装依赖

### 单个包

```bash
# pnpm
pnpm add lodash --filter=@repo/utils

# npm
npm install lodash --workspace=@repo/utils

# yarn
yarn workspace @repo/utils add lodash

# bun
cd packages/utils && bun add lodash
```

### 多个包

```bash
# pnpm
pnpm add jest --save-dev --filter=web --filter=@repo/ui

# npm
npm install jest --save-dev --workspace=web --workspace=@repo/ui

# yarn (v2+)
yarn workspaces foreach -R --from '{web,@repo/ui}' add jest --dev
```

### 内部包

```bash
# pnpm
pnpm add @repo/ui --filter=web

# 这会更新 package.json：
{
  "dependencies": {
    "@repo/ui": "workspace:*"
  }
}
```

## 保持版本同步

### 选项 1：工具

```bash
# syncpack - 检查和修复版本不匹配
npx syncpack list-mismatches
npx syncpack fix-mismatches

# manypkg - 类似功能
npx @manypkg/cli check
npx @manypkg/cli fix

# sherif - 基于 Rust，非常快
npx sherif
```

### 选项 2：包管理器命令

```bash
# pnpm - 到处更新
pnpm up --recursive typescript@latest

# npm - 在所有工作区中更新
npm install typescript@latest --workspaces
```

### 选项 3：pnpm 目录（pnpm 9.5+）

```yaml
# pnpm-workspace.yaml
packages:
  - "apps/*"
  - "packages/*"

catalog:
  react: ^18.2.0
  typescript: ^5.3.0
```

```json
// 任何 package.json
{
  "dependencies": {
    "react": "catalog:" // 使用目录中的版本
  }
}
```

## 内部 vs 外部依赖

### 内部（工作区）

```json
// pnpm/bun
{ "@repo/ui": "workspace:*" }

// npm/yarn
{ "@repo/ui": "*" }
```

Turborepo 理解这些关系并相应地排序构建。

### 外部（npm 注册表）

```json
{ "lodash": "^4.17.21" }
```

来自 npm 的标准 semver 版本控制。

### Peer 依赖

对于期望使用者提供依赖的库包：

```json
// packages/ui/package.json
{
  "peerDependencies": {
    "react": "^18.0.0",
    "react-dom": "^18.0.0"
  },
  "devDependencies": {
    "react": "^18.0.0", // 用于开发/测试
    "react-dom": "^18.0.0"
  }
}
```

## 常见问题

### "Module not found"

1. 检查依赖是否安装在正确的包中
2. 运行 `pnpm install` / `npm install` 更新锁文件
3. 检查包中是否定义了导出

### 版本冲突

包可以使用不同版本 - 这是一个功能，不是错误。但如果您需要一致性：

1. 使用工具（syncpack、manypkg）
2. 使用 pnpm 目录
3. 创建 lint 规则

### 提升问题

某些工具期望依赖项在特定位置。使用包管理器配置：

```yaml
# .npmrc (pnpm)
public-hoist-pattern[]=*eslint*
public-hoist-pattern[]=*prettier*
```

## 锁文件

对于以下情况是**必需的**：

- 可重现的构建
- Turborepo 依赖分析
- 缓存正确性

```bash
# 提交您的锁文件！
git add pnpm-lock.yaml  # 或 package-lock.json、yarn.lock
```
