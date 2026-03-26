---
name: pnpm-cli-commands
description: 用于包管理、运行脚本和工作区操作的基本 pnpm 命令
---

# pnpm CLI 命令

pnpm 提供了一个全面的 CLI 用于包管理，命令类似于 npm/yarn，但具有独特的功能。

## 安装命令

### 安装所有依赖
```bash
pnpm install
# 或
pnpm i
```

### 添加依赖
```bash
# 生产依赖
pnpm add <pkg>

# 开发依赖
pnpm add -D <pkg>
pnpm add --save-dev <pkg>

# 可选依赖
pnpm add -O <pkg>

# 全局包
pnpm add -g <pkg>

# 特定版本
pnpm add <pkg>@<version>
pnpm add <pkg>@next
pnpm add <pkg>@^1.0.0
```

### 删除依赖
```bash
pnpm remove <pkg>
pnpm rm <pkg>
pnpm uninstall <pkg>
pnpm un <pkg>
```

### 更新依赖
```bash
# 更新所有
pnpm update
pnpm up

# 更新特定包
pnpm update <pkg>

# 更新到最新版本（忽略 semver）
pnpm update --latest
pnpm up -L

# 交互式更新
pnpm update --interactive
pnpm up -i
```

## 脚本命令

### 运行脚本
```bash
pnpm run <script>
# 或简写
pnpm <script>

# 传递参数给脚本
pnpm run build -- --watch

# 如果脚本存在则运行（不存在时不报错）
pnpm run --if-present build
```

### 执行二进制文件
```bash
# 运行本地二进制文件
pnpm exec <command>

# 示例
pnpm exec eslint .
```

### dlx - 无需安装即可运行
```bash
# 类似 npx，但是用于 pnpm
pnpm dlx <pkg>

# 示例
pnpm dlx create-vite my-app
pnpm dlx degit user/repo my-project
```

## 工作区命令

### 在所有包中运行
```bash
# 在所有工作区包中运行脚本
pnpm -r run <script>
pnpm --recursive run <script>

# 在特定包中运行
pnpm --filter <pattern> run <script>

# 示例
pnpm --filter "./packages/**" run build
pnpm --filter "!./packages/internal/**" run test
pnpm --filter "@myorg/*" run lint
```

### 过滤模式
```bash
# 按包名
pnpm --filter <pkg-name> <command>
pnpm --filter "@scope/pkg" build

# 按目录
pnpm --filter "./packages/core" test

# 包的依赖项
pnpm --filter "...@scope/app" build

# 包的依赖者
pnpm --filter "@scope/core..." test

# 自提交/分支以来更改的包
pnpm --filter "...[origin/main]" build
```

## 其他有用命令

### 链接包
```bash
# 链接全局包
pnpm link --global
pnpm link -g

# 使用链接的包
pnpm link --global <pkg>
```

### 补丁包
```bash
# 为包创建补丁
pnpm patch <pkg>@<version>

# 编辑后，提交补丁
pnpm patch-commit <path>

# 删除补丁
pnpm patch-remove <pkg>
```

### 存储管理
```bash
# 显示存储路径
pnpm store path

# 删除未引用的包
pnpm store prune

# 检查存储完整性
pnpm store status
```

### 其他命令
```bash
# 清洁安装（类似 npm ci）
pnpm install --frozen-lockfile

# 列出已安装的包
pnpm list
pnpm ls

# 为什么安装了这个包？
pnpm why <pkg>

# 过时的包
pnpm outdated

# 审计漏洞
pnpm audit

# 重新构建原生模块
pnpm rebuild

# 从 npm/yarn lockfile 导入
pnpm import

# 创建 tarball
pnpm pack

# 发布包
pnpm publish
```

## 有用的标志

```bash
# 忽略脚本
pnpm install --ignore-scripts

# 优先离线（使用缓存）
pnpm install --prefer-offline

# 严格的同伴依赖
pnpm install --strict-peer-dependencies

# 仅生产环境
pnpm install --prod
pnpm install -P

# 无可选依赖
pnpm install --no-optional
```

<!--
源引用:
- https://pnpm.io/cli/install
- https://pnpm.io/cli/add
- https://pnpm.io/cli/run
- https://pnpm.io/filtering
-->
