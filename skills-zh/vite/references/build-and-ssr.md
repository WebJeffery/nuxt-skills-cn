---
name: vite-build-ssr
description: Vite 库模式和 SSR 配置
---

# 构建与 SSR

## 库模式

构建用于分发的库：

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

### 多个入口

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

- 单个入口：`es` 和 `umd`
- 多个入口：`es` 和 `cjs`

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

### 中间件模式

在自定义服务器中将 Vite 用作中间件：

```ts
import express from 'express'
import { createServer as createViteServer } from 'vite'

const app = express()

const vite = await createViteServer({
  server: { middlewareMode: true },
  appType: 'custom',
})

app.use(vite.middlewares)

app.use('*all', async (req, res, next) => {
  const url = req.originalUrl

  // 1. 读取并转换 index.html
  let template = await fs.readFile('index.html', 'utf-8')
  template = await vite.transformIndexHtml(url, template)

  // 2. 加载服务器入口
  const { render } = await vite.ssrLoadModule('/src/entry-server.ts')

  // 3. 渲染应用
  const appHtml = await render(url)

  // 4. 注入到模板中
  const html = template.replace('<!--ssr-outlet-->', appHtml)

  res.status(200).set({ 'Content-Type': 'text/html' }).end(html)
})

app.listen(5173)
```

### SSR 构建

```json
{
  "scripts": {
    "build:client": "vite build --outDir dist/client",
    "build:server": "vite build --outDir dist/server --ssr src/entry-server.ts"
  }
}
```

`--ssr` 标志：
- 默认外部化依赖
- 输出供 Node.js 使用

### SSR 清单

生成资源映射以进行预加载提示：

```bash
vite build --outDir dist/client --ssrManifest
```

创建 `dist/client/.vite/ssr-manifest.json`，将模块 ID 映射到代码块。

### SSR 外部化

控制哪些依赖被打包与外部化：

```ts
export default defineConfig({
  ssr: {
    noExternal: ['some-package'],  // 打包此依赖
    external: ['another-package'], // 外部化此依赖
  },
})
```

### 条件逻辑

```ts
if (import.meta.env.SSR) {
  // 仅服务器代码（从客户端 tree-shake）
}
```

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
// 加载所有环境变量（空前缀 = 无过滤）
```

<!--
Source references:
- https://vite.dev/guide/build
- https://vite.dev/guide/ssr
- https://vite.dev/guide/api-javascript
-->
