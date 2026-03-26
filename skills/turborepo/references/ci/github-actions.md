# GitHub Actions

Turborepo 与 GitHub Actions 的完整设置指南。

## 基本工作流结构

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 2

      - uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm ci

      - name: Build and Test
        run: turbo run build test lint
```

## 包管理器设置

### pnpm

```yaml
- uses: pnpm/action-setup@v3
  with:
    version: 9

- uses: actions/setup-node@v4
  with:
    node-version: 20
    cache: "pnpm"

- run: pnpm install --frozen-lockfile
```

### Yarn

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: 20
    cache: "yarn"

- run: yarn install --frozen-lockfile
```

### Bun

```yaml
- uses: oven-sh/setup-bun@v1
  with:
    bun-version: latest

- run: bun install --frozen-lockfile
```

## 远程缓存设置

### 1. 创建 Vercel 访问令牌

1. 转到 [Vercel 仪表板](https://vercel.com/account/tokens)
2. 创建具有适当范围的新令牌
3. 复制令牌值

### 2. 添加密钥和变量

在你的 GitHub 仓库设置中：

**密钥**（设置 > 密钥和变量 > 操作 > 密钥）：

- `TURBO_TOKEN`：你的 Vercel 访问令牌

**变量**（设置 > 密钥和变量 > 操作 > 变量）：

- `TURBO_TEAM`：你的 Vercel 团队 slug

### 3. 添加到工作流

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    env:
      TURBO_TOKEN: ${{ secrets.TURBO_TOKEN }}
      TURBO_TEAM: ${{ vars.TURBO_TEAM }}
```

## 替代方案：actions/cache

如果你无法使用远程缓存，缓存 Turborepo 的本地缓存目录：

```yaml
- uses: actions/cache@v4
  with:
    path: .turbo
    key: turbo-${{ runner.os }}-${{ hashFiles('**/turbo.json', '**/package-lock.json') }}
    restore-keys: |
      turbo-${{ runner.os }}-
```

注意：这比远程缓存效果差，因为它是每个分支的。

## 完整示例

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      TURBO_TOKEN: ${{ secrets.TURBO_TOKEN }}
      TURBO_TEAM: ${{ vars.TURBO_TEAM }}

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 2

      - uses: pnpm/action-setup@v3
        with:
          version: 9

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "pnpm"

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Build
        run: turbo run build --affected

      - name: Test
        run: turbo run test --affected

      - name: Lint
        run: turbo run lint --affected
```