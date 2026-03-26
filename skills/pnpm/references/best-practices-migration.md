---
name: migration-to-pnpm
description: 从 npm 或 Yarn 迁移到 pnpm，最小化摩擦
---

# 迁移到 pnpm

从 npm 或 Yarn 迁移现有项目到 pnpm 的指南。

## 快速迁移

### 从 npm

```bash
# 删除 npm lockfile 和 node_modules
rm -rf node_modules package-lock.json

# 使用 pnpm 安装
pnpm install
```

### 从 Yarn

```bash
# 删除 yarn lockfile 和 node_modules
rm -rf node_modules yarn.lock

# 使用 pnpm 安装
pnpm install
```

### 导入现有 Lockfile

pnpm 可以导入现有 lockfile：

```bash
# 从 npm 或 yarn lockfile 导入
pnpm import

# 这会从以下内容创建 pnpm-lock.yaml：
# - package-lock.json (npm)
# - yarn.lock (yarn)
# - npm-shrinkwrap.json (npm)
```

## 处理常见问题

### 幽灵依赖

pnpm 对依赖很严格。如果代码导入了不在 `package.json` 中的包，它将失败。

**问题：**
```js
// 在 npm 中有效（提升），在 pnpm 中失败
import lodash from 'lodash' // 不在依赖中，由另一个包安装
```

**解决方案：** 显式添加缺失的依赖：
```bash
pnpm add lodash
```

### 缺失同伴依赖

pnpm 默认报告同伴依赖问题。

**选项 1：** 让 pnpm 自动安装：
```ini
# .npmrc（pnpm v8+ 中的默认设置）
auto-install-peers=true
```

**选项 2：** 手动安装：
```bash
pnpm add react react-dom
```

**选项 3：** 如果可接受则抑制警告：
```json
{
  "pnpm": {
    "peerDependencyRules": {
      "ignoreMissing": ["react"]
    }
  }
}
```

### 符号链接问题

某些工具不适用于符号链接。使用提升模式：

```ini
# .npmrc
node-linker=hoisted
```

或提升特定包：

```ini
public-hoist-pattern[]=*eslint*
public-hoist-pattern[]=*babel*
```

### 原生模块重新构建

如果原生模块失败，请尝试：

```bash
# 重新构建所有原生模块
pnpm rebuild

# 或重新安装
rm -rf node_modules
pnpm install
```

## Monorepo 迁移

### 从 npm Workspaces

1. 创建 `pnpm-workspace.yaml`：
   ```yaml
   packages:
     - 'packages/*'
   ```

2. 更新内部依赖以使用工作区协议：
   ```json
   {
     "dependencies": {
       "@myorg/utils": "workspace:^"
     }
   }
   ```

3. 安装：
   ```bash
   rm -rf node_modules packages/*/node_modules package-lock.json
   pnpm install
   ```

### 从 Yarn Workspaces

1. 删除 Yarn 特定文件：
   ```bash
   rm yarn.lock .yarnrc.yml
   rm -rf .yarn
   ```

2. 创建 `pnpm-workspace.yaml` 匹配 package.json 中的 `workspaces`：
   ```yaml
   packages:
     - 'packages/*'
   ```

3. 更新 `package.json` - 如果不需要则删除 Yarn 工作区配置：
   ```json
   {
     // 删除 "workspaces" 字段（可选，pnpm 使用 pnpm-workspace.yaml）
   }
   ```

4. 转换工作区引用：
   ```json
   // 从 Yarn
   "@myorg/utils": "*"
   
   // 到 pnpm
   "@myorg/utils": "workspace:*"
   ```

### 从 Lerna

pnpm 可以在大多数用例中替换 Lerna：

```bash
# Lerna：在所有包中运行脚本
lerna run build

# pnpm 等效
pnpm -r run build

# Lerna：在特定包中运行
lerna run build --scope=@myorg/app

# pnpm 等效
pnpm --filter @myorg/app run build

# Lerna：发布
lerna publish

# pnpm：使用 changesets 代替
pnpm add -Dw @changesets/cli
pnpm changeset
pnpm changeset version
pnpm publish -r
```

## 配置迁移

### .npmrc 设置

大多数 npm/Yarn 设置在 pnpm 的 `.npmrc` 中有效：

```ini
# 注册表设置（与 npm 相同）
registry=https://registry.npmjs.org/
@myorg:registry=https://npm.myorg.com/

# 认证令牌（与 npm 相同）
//registry.npmjs.org/:_authToken=${NPM_TOKEN}

# pnpm 特定添加
auto-install-peers=true
strict-peer-dependencies=false
```

### 脚本迁移

大多数脚本无需更改即可工作。更新 pnpm 特定模式：

```json
{
  "scripts": {
    // npm：递归脚本
    "build:all": "npm run build --workspaces",
    // pnpm：使用 -r 标志
    "build:all": "pnpm -r run build",
    
    // npm：在特定工作区中运行
    "dev:app": "npm run dev -w packages/app",
    // pnpm：使用 --filter
    "dev:app": "pnpm --filter @myorg/app run dev"
  }
}
```

## CI/CD 迁移

更新 CI 配置：

```yaml
# 之前 (npm)
- run: npm ci

# 之后 (pnpm)
- uses: pnpm/action-setup@v4
- run: pnpm install --frozen-lockfile
```

为 Corepack 添加到 `package.json`：
```json
{
  "packageManager": "pnpm@9.0.0"
}
```

## 逐步迁移

对于大型项目，逐步迁移：

1. **从 CI 开始**：在 CI 中使用 pnpm，本地保留 npm/yarn
2. **添加 pnpm-lock.yaml**：运行 `pnpm import` 创建 lockfile
3. **彻底测试**：确保构建与 pnpm 一起工作
4. **更新文档**：更新 README、CONTRIBUTING
5. **删除旧文件**：团队采用后删除旧 lockfile

## 回滚计划

如果迁移导致问题：

```bash
# 删除 pnpm 文件
rm -rf node_modules pnpm-lock.yaml pnpm-workspace.yaml

# 恢复 npm
npm install

# 或恢复 Yarn
yarn install
```

在 git 历史中保留旧 lockfile 以便于回滚。

<!--
源引用:
- https://pnpm.io/installation
- https://pnpm.io/cli/import
- https://pnpm.io/limitations
-->
