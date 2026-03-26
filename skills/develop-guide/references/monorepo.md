---
name: monorepo
description: 使用 pnpm 工作区、集中别名和 Turborepo 设置 Monorepo。用于创建或管理多包存储库时。
---

# Monorepo 设置

## pnpm 工作区

使用 pnpm 工作区进行 monorepo 管理：

```yaml
# pnpm-workspace.yaml
packages:
  - 'packages/*'
```

## 脚本约定

在每个包中都有脚本，并在根目录使用 `-r`（递归）标志，
为 monorepo 中的更快 linting 启用 ESLint 缓存。

```json
// 根 package.json
{
  "scripts": {
    "build": "pnpm run -r build",
    "test": "vitest",
    "lint": "eslint . --cache --concurrency=auto"
  }
}
```

在每个包的 `package.json` 中，添加脚本。

```json
// packages/*/package.json
{
  "scripts": {
    "build": "tsdown",
    "prepack": "pnpm build"
  }
}
```

## ESLint 缓存

```json
{
  "scripts": {
    "lint": "eslint . --cache --concurrency=auto"
  }
}
```

## Turborepo（可选）

对于具有多个包或构建时间长的 monorepo，使用 Turborepo 进行任务编排和缓存。

有关详细配置，请参阅专门的 Turborepo 技能。

## 集中别名

为了在 Vite、Nuxt、Vitest 配置中获得更好的 DX，在项目根目录创建集中的 `alias.ts`：

```ts
// alias.ts
import fs from 'node:fs'
import { fileURLToPath } from 'node:url'
import { join, relative } from 'pathe'

const root = fileURLToPath(new URL('.', import.meta.url))
const r = (path: string) => fileURLToPath(new URL(`./packages/${path}`, import.meta.url))

export const alias = {
  '@myorg/core': r('core/src/index.ts'),
  '@myorg/utils': r('utils/src/index.ts'),
  '@myorg/ui': r('ui/src/index.ts'),
  // 根据需要添加更多别名
}

// 自动更新 tsconfig.alias.json 路径
const raw = fs.readFileSync(join(root, 'tsconfig.alias.json'), 'utf-8').trim()
const tsconfig = JSON.parse(raw)
tsconfig.compilerOptions.paths = Object.fromEntries(
  Object.entries(alias).map(([key, value]) => [key, [`./${relative(root, value)}`]]),
)
const newRaw = JSON.stringify(tsconfig, null, 2)
if (newRaw !== raw)
  fs.writeFileSync(join(root, 'tsconfig.alias.json'), `${newRaw}\n`, 'utf-8')
```

然后更新 `tsconfig.json` 以使用别名文件：

```json
{
  "extends": [
    "./tsconfig.alias.json"
  ]
}
```

### 在配置中使用别名

在所有配置文件中引用集中别名：

```ts
// vite.config.ts
import { alias } from './alias'

export default defineConfig({
  resolve: { alias },
})
```

```ts
// nuxt.config.ts
import { alias } from './alias'

export default defineNuxtConfig({
  alias,
})
```
