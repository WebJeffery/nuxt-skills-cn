---
name: vite-build-ssr
description: Vite 库模式、多页应用、JavaScript API 和 SSR 指南
---

# 构建和 SSR

## 库模式

构建用于分发的库:

```ts
// vite.config.ts
import { resolve } from 'node:path'
import { defineConfig } from 'vite'

export default defineConfig({
  build: {
    lib: {
      entry: resolve(import.meta.dirname, 'lib/main.ts'),
      name: 'MyLib',
      fileName: 'my-lib',
    },
    rolldownOptions: {
      external: ['vue', 'react'],
      output: {
        globals: {
          vue: 'Vue',
          react: 'React',
        },
      },
    },
  },
})
```

### 多入口

```ts
build: {
  lib: {
    entry: {
      'my-lib': resolve(import.meta.dirname, 'lib/main.ts'),
      secondary: resolve(import.meta.dirname, 'lib/secondary.ts'),
    },
    name: 'MyLib',
  },
}
```

### 输出格式

- 单入口: `es` 和 `umd`
- 多入口: `es` 和 `cjs`

### Package.json 设置

```json
{
  "name": "my-lib",
  "type": "module",
  "files": ["dist"],
  "main": "./dist/my-lib.umd.cjs",
  "module": "./dist/my-lib.js",
  "exports": {
    ".": {
      "import": "./dist/my-lib.js",
      "require": "./dist/my-lib.umd.cjs"
    },
    "./style.css": "./dist/my-lib.css"
  }
}
```

## 多页应用

```ts
export default defineConfig({
  build: {
    rolldownOptions: {
      input: {
        main: resolve(import.meta.dirname, 'index.html'),
        nested: resolve(import.meta.dirname, 'nested/index.html'),
      },
    },
  },
})
```

## SSR 开发

**注意:** Vite 的 SSR 支持**是低级别的**,主要面向元框架作者,而不是应用开发者。如果你的应用需要 SSR,请使用基于 Vite 的元框架:

- **Nuxt** (Vue) - https://nuxt.com
- **SvelteKit** (Svelte) - https://svelte.dev/docs/kit
- **SolidStart** (Solid) - https://start.solidjs.com
- **TanStack Start** (React) - https://tanstack.com/start

这些框架构建在 Vite 的 SSR 原语之上,因此您不必自己连接它们。

**需要服务器?** 考虑 [Nitro](https://nitro.build) -- 将其视为"服务器的 Vite"。Nitro 提供可移植的、框架无关的服务器层,具有基于文件的 API 路由、自动导入和数十个平台的部署预设(Node.js、Deno、Bun、Cloudflare Workers、Vercel、Netlify 等)。它与 Vite 自然集成,并为 Nuxt 的服务器引擎提供动力。有关更多详细信息,请参阅 [Nitro 文档](https://nitro.build)。

## JavaScript API

### createServer

```ts
import { createServer } from 'vite'

const server = await createServer({
  configFile: false,
  root: import.meta.dirname,
  server: { port: 1337 },
})

await server.listen()
server.printUrls()
```

### build

```ts
import { build } from 'vite'

await build({
  root: './project',
  build: { outDir: 'dist' },
})
```

### preview

```ts
import { preview } from 'vite'

const previewServer = await preview({
  preview: { port: 8080, open: true },
})
previewServer.printUrls()
```

### resolveConfig

```ts
import { resolveConfig } from 'vite'

const config = await resolveConfig({}, 'build')
```

### loadEnv

```ts
import { loadEnv } from 'vite'

const env = loadEnv('development', process.cwd(), '')
// 加载所有环境变量(空前缀 = 无过滤)
```

<!--
Source references:
- https://vite.dev/guide/build
- https://vite.dev/guide/api-javascript
- https://nitro.build
-->
