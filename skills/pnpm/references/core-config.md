---
name: pnpm-configuration
description: 通过 pnpm-workspace.yaml 和 .npmrc 设置的配置选项
---

# pnpm 配置

pnpm 使用两个主要配置文件：`pnpm-workspace.yaml` 用于工作区和 pnpm 特定设置，`.npmrc` 用于 npm 兼容和 pnpm 特定设置。

## pnpm-workspace.yaml

pnpm 特定配置的推荐位置。放置在项目根目录。

```yaml
# 定义工作区包
packages:
  - 'packages/*'
  - 'apps/*'
  - '!**/test/**'  # 排除模式

# 用于共享依赖版本的目录
catalog:
  react: ^18.2.0
  typescript: ~5.3.0

# 用于不同依赖组的命名目录
catalogs:
  react17:
    react: ^17.0.2
    react-dom: ^17.0.2
  react18:
    react: ^18.2.0
    react-dom: ^18.2.0

# 覆盖解析（首选位置）
overrides:
  lodash: ^4.17.21
  'foo@^1.0.0>bar': ^2.0.0

# pnpm 设置（.npmrc 的替代方案）
settings:
  auto-install-peers: true
  strict-peer-dependencies: false
  link-workspace-packages: true
  prefer-workspace-packages: true
  shared-workspace-lockfile: true
```

## .npmrc 设置

pnpm 从 `.npmrc` 文件读取设置。在项目根目录或用户主目录创建。

### 常见的 pnpm 设置

```ini
# 自动安装同伴依赖
auto-install-peers=true

# 同伴依赖问题时失败
strict-peer-dependencies=false

# 依赖的提升模式
public-hoist-pattern[]=*types*
public-hoist-pattern[]=*eslint*
shamefully-hoist=false

# 存储位置
store-dir=~/.pnpm-store

# 虚拟存储位置
virtual-store-dir=node_modules/.pnpm

# 锁文件设置
lockfile=true
prefer-frozen-lockfile=true

# 副作用缓存（加速重新构建）
side-effects-cache=true

# 注册表设置
registry=https://registry.npmjs.org/
@myorg:registry=https://npm.myorg.com/
```

### 工作区设置

```ini
# 链接工作区包
link-workspace-packages=true

# 优先使用工作区包而非注册表
prefer-workspace-packages=true

# 所有包使用单个锁文件
shared-workspace-lockfile=true

# 工作区依赖的保存前缀
save-workspace-protocol=rolling
```

### Node.js 设置

```ini
# 使用特定的 Node.js 版本
use-node-version=20.10.0

# Node.js 版本文件
node-version-file=.nvmrc

# 管理 Node.js 版本
manage-package-manager-versions=true
```

### 安全设置

```ini
# 忽略特定脚本
ignore-scripts=false

# 允许特定的构建脚本
onlyBuiltDependencies[]=esbuild
onlyBuiltDependencies[]=sharp

# 用于缺失同伴依赖的包扩展
package-extensions[foo@1].peerDependencies.bar=*
```

## 配置层次结构

设置按顺序读取（后面的覆盖前面的）：

1. `/etc/npmrc` - 全局配置
2. `~/.npmrc` - 用户配置
3. `<project>/.npmrc` - 项目配置
4. 环境变量：`npm_config_<key>=<value>`
5. `pnpm-workspace.yaml` settings 字段

## 环境变量

```bash
# 通过环境变量设置配置
npm_config_registry=https://registry.npmjs.org/

# pnpm 特定的环境变量
PNPM_HOME=~/.local/share/pnpm
```

## Package.json 字段

pnpm 从 `package.json` 读取特定字段：

```json
{
  "pnpm": {
    "overrides": {
      "lodash": "^4.17.21"
    },
    "peerDependencyRules": {
      "ignoreMissing": ["@babel/*"],
      "allowedVersions": {
        "react": "17 || 18"
      }
    },
    "neverBuiltDependencies": ["fsevents"],
    "onlyBuiltDependencies": ["esbuild"],
    "allowedDeprecatedVersions": {
      "request": "*"
    },
    "patchedDependencies": {
      "express@4.18.2": "patches/express@4.18.2.patch"
    }
  }
}
```

## 与 npm/yarn 的主要区别

1. **默认严格**：没有幽灵依赖
2. **工作区协议**：`workspace:*` 用于本地包
3. **目录**：集中式版本管理
4. **内容寻址存储**：跨项目共享

<!--
源引用:
- https://pnpm.io/pnpm-workspace_yaml
- https://pnpm.io/npmrc
- https://pnpm.io/package_json
-->
