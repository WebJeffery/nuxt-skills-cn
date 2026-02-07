---
name: pnpm-overrides
description: 强制依赖的特定版本，包括传递依赖
---

# pnpm 覆盖

覆盖允许您强制包的特定版本，包括传递依赖。用于修复安全漏洞或兼容性问题。

## 基本语法

在 `pnpm-workspace.yaml`（推荐）或 `package.json` 中定义覆盖：

### 在 pnpm-workspace.yaml 中（推荐）

```yaml
packages:
  - 'packages/*'

overrides:
  # 覆盖包的所有版本
  lodash: ^4.17.21
  
  # 覆盖特定版本范围
  "foo@^1.0.0": ^1.2.3
  
  # 覆盖嵌套依赖
  "express>cookie": ^0.6.0
  
  # 覆盖到不同的包
  "underscore": "npm:lodash@^4.17.21"
```

### 在 package.json 中

```json
{
  "pnpm": {
    "overrides": {
      "lodash": "^4.17.21",
      "foo@^1.0.0": "^1.2.3",
      "bar@^2.0.0>qux": "^1.0.0"
    }
  }
}
```

## 覆盖模式

### 覆盖所有实例
```yaml
overrides:
  lodash: ^4.17.21
```
强制所有 lodash 安装使用 ^4.17.21。

### 覆盖特定父版本
```yaml
overrides:
  "foo@^1.0.0": ^1.2.3
```
仅当请求的版本匹配 ^1.0.0 时覆盖 foo。

### 覆盖嵌套依赖
```yaml
overrides:
  "express>cookie": ^0.6.0
  "foo@1.x>bar@^2.0.0>qux": ^1.0.0
```
仅当 cookie 是 express 的依赖时才覆盖它。

### 替换为不同的包
```yaml
overrides:
  # 用 lodash 替换 underscore
  "underscore": "npm:lodash@^4.17.21"
  
  # 使用本地文件
  "some-pkg": "file:./local-pkg"
  
  # 使用 git
  "some-pkg": "github:user/repo#commit"
```

### 删除依赖
```yaml
overrides:
  "unwanted-pkg": "-"
```
`-` 完全删除包。

## 常见用例

### 安全修复

强制修补易受攻击包的版本：

```yaml
overrides:
  # 修复传递依赖中的 CVE
  "minimist": "^1.2.6"
  "json5": "^2.2.3"
```

### 去重依赖

当安装多个版本时强制单个版本：

```yaml
overrides:
  "react": "^18.2.0"
  "react-dom": "^18.2.0"
```

### 修复同伴依赖问题

```yaml
overrides:
  "@types/react": "^18.2.0"
```

### 替换已弃用的包

```yaml
overrides:
  "request": "npm:@cypress/request@^3.0.0"
```

## 钩子替代方案

对于更复杂的场景，使用 `.pnpmfile.cjs`：

```js
// .pnpmfile.cjs
function readPackage(pkg, context) {
  // 覆盖依赖版本
  if (pkg.dependencies?.lodash) {
    pkg.dependencies.lodash = '^4.17.21'
  }
  
  // 添加缺失的同伴依赖
  if (pkg.name === 'some-package') {
    pkg.peerDependencies = {
      ...pkg.peerDependencies,
      react: '*'
    }
  }
  
  return pkg
}

module.exports = {
  hooks: {
    readPackage
  }
}
```

## 覆盖与目录

| 功能 | 覆盖 | 目录 |
|---------|-----------|----------|
| 影响 | 所有依赖（包括传递依赖） | 仅直接依赖 |
| 用法 | 自动 | 显式 `catalog:` 引用 |
| 目的 | 强制版本、修复问题 | 版本管理 |
| 粒度 | 可以针对特定父级 | 仅包范围 |

## 调试

检查解析的版本：

```bash
# 查看解析的版本
pnpm why lodash

# 列出所有版本
pnpm list lodash --depth=Infinity
```

<!--
源引用:
- https://pnpm.io/package_json#pnpmoverrides
- https://pnpm.io/pnpmfile
-->
