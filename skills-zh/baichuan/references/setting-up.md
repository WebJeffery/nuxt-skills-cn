---
name: setting-up
description: 项目设置文件，包括 .gitignore、GitHub Actions 工作流和 VS Code 扩展。用于初始化新项目或添加 CI/编辑器配置。
---

# 项目设置

## .gitignore

当 `.gitignore` 不存在时创建:

```
*.log
*.tgz
.cache
.DS_Store
.eslintcache
.idea
.env
.nuxt
.temp
.output
.turbo
cache
coverage
dist
lib-cov
logs
node_modules
temp
```

## GitHub Actions

设置新项目时添加这些工作流。如果工作流已存在则跳过。所有工作流都使用 [sxzz/workflows](https://github.com/sxzz/workflows) 可重用工作流。

### 自动修复工作流

**`.github/workflows/autofix.yml`** - 在 PR 上自动修复 linting:

```yaml
name: autofix.ci

on: [pull_request]

jobs:
  autofix:
    uses: sxzz/workflows/.github/workflows/autofix.yml@v1
    permissions:
      contents: read
```

### 单元测试工作流

**`.github/workflows/unit-test.yml`** - 在 push/PR 时运行测试:

```yaml
name: Unit Test

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions: {}

jobs:
  unit-test:
    uses: sxzz/workflows/.github/workflows/unit-test.yml@v1
```

### 发布工作流

**`.github/workflows/release.yml`** - 在 tag 时发布 (仅限库项目):

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    uses: sxzz/workflows/.github/workflows/release.yml@v1
    with:
      publish: true
    permissions:
      contents: write
      id-token: write
```

## VS Code 扩展

在 `.vscode/extensions.json` 中配置:

```json
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "antfu.pnpm-catalog-lens",
    "antfu.iconify",
    "antfu.unocss",
    "antfu.slidev",
    "vue.volar"
  ]
}
```

| 扩展 | 描述 |
|-----------|-------------|
| `dbaeumer.vscode-eslint` | ESLint 集成，用于 linting 和格式化 |
| `antfu.pnpm-catalog-lens` | 内联显示 pnpm catalog 版本提示 |
| `antfu.iconify` | Iconify 图标预览和自动完成 |
| `antfu.unocss` | UnoCSS IntelliSense 和语法高亮 |
| `antfu.slidev` | Slidev 预览和语法高亮 |
| `vue.volar` | Vue 语言功能 |
