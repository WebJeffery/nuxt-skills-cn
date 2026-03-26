---
name: vitepress-ssr
description: 服务器端渲染配置、自定义服务器、预渲染和 SSR 优化
---

# 服务器端渲染 (SSR)

VitePress 默认为静态站点生成器(SSG),但支持自定义 SSR。

## SSG 与 SSR

**SSG (默认):**
- 在构建时预渲染所有路由
- 生成静态 HTML 文件
- 快速,易于部署到任何静态主机

**SSR:**
- 在服务器上动态渲染页面
- 支持动态内容
- 需要自定义服务器

## 自定义 SSR

### 创建自定义服务器

```ts
// server.js
import fs from 'node:fs'
import path from 'node:path'
import { fileURLToPath } from 'node:url'
import { createServer as createViteServer } from 'vite'

const __dirname = path.dirname(fileURLToPath(import.meta.url))

async function createServer() {
  const vite = await createViteServer({
    configFile: path.resolve(__dirname, '.vitepress/config.ts'),
    server: { middlewareMode: true }
  })
  
  const { render } = await vite.ssrLoadModule('/.vitepress/temp/app.js')
  
  return async (req, res) => {
    try {
      const url = req.url
      const { app } = await render(url)
      const html = await app.renderToString()
      
      const template = fs.readFileSync(
        path.resolve(__dirname, '.vitepress/dist/client/index.html'),
        'utf-8'
      )
      
      const transformedHtml = template
        .replace(`<!--app-html-->`, html)
        .replace(
          `<script type="module" src="/.vitepress/dist/client/app.js"></script>`,
          ''
        )
      
      res.setHeader('Content-Type', 'text/html')
      res.end(transformedHtml)
    } catch (e) {
      vite.ssrFixStacktrace(e)
      console.error(e)
      res.statusCode = 500
      res.end(e.message)
    }
  }
}
```

### 使用 Express

```ts
// server.js
import express from 'express'
import fs from 'node:fs'
import path from 'node:path'
import { createServer as createViteServer } from 'vite'

const app = express()
const vite = await createViteServer({
  server: { middlewareMode: true }
})

app.use(vite.middlewares)

app.use('*', async (req, res) => {
  try {
    const { render } = await vite.ssrLoadModule('/.vitepress/temp/app.js')
    const { app } = await render(req.url)
    const html = await app.renderToString()
    
    const template = fs.readFileSync(
      path.resolve('.vitepress/dist/client/index.html'),
      'utf-8'
    )
    
    res.send(template.replace(`<!--app-html-->`, html))
  } catch (e) {
    vite.ssrFixStacktrace(e)
    console.error(e)
    res.status(500).end(e.message)
  }
})

app.listen(3000)
```

## 预渲染

### 预渲染特定路由

```ts
// .vitepress/config.ts
export default {
  build: {
    ssr: true,
    // 预渲染这些路由
    prerender: {
      routes: [
        '/',
        '/guide/',
        '/guide/getting-started'
      ]
    }
  }
}
```

### 动态预渲染

```ts
// .vitepress/config.ts
export default {
  build: {
    prerender: {
      routes: async () => {
        // 从 API 或文件系统获取路由
        const posts = await fetch('https://api.example.com/posts').then(r => r.json())
        return posts.map(post => `/posts/${post.slug}`)
      }
    }
  }
}
```

## SSR 优化

### 服务器缓存

```ts
// server.js
import LRU from 'lru-cache'

const ssrCache = new LRU({
  max: 1000,
  ttl: 1000 * 60 * 15 // 15 分钟
})

app.use('*', async (req, res) => {
  const cacheKey = req.url
  
  if (ssrCache.has(cacheKey)) {
    return res.send(ssrCache.get(cacheKey))
  }
  
  const html = await render(req.url)
  ssrCache.set(cacheKey, html)
  
  res.send(html)
})
```

### 流式渲染

```ts
// server.js
import { renderToString } from 'vue/server-renderer'

app.use('*', async (req, res) => {
  const { app } = await render(req.url)
  
  res.setHeader('Content-Type', 'text/html')
  
  const stream = renderToNodeStream(app)
  stream.pipe(res)
})
```

## 处理客户端特定代码

使用 `ClientOnly` 组件:

```vue
<ClientOnly>
  <HeavyComponent />
</ClientOnly>
```

或使用 `import.meta.env.SSR`:

```vue
<script setup>
import { ref, onMounted } from 'vue'

const data = ref(null)

onMounted(async () => {
  if (!import.meta.env.SSR) {
    data.value = await fetch('/api/data').then(r => r.json())
  }
})
</script>
```

## 环境变量

```ts
// .vitepress/config.ts
export default {
  vite: {
    define: {
      'import.meta.env.SSR': JSON.stringify(true)
    }
  }
}
```

## 部署

### Vercel

```js
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": ".vitepress/dist",
  "devCommand": "npm run dev"
}
```

### Netlify

```toml
# netlify.toml
[build]
  command = "npm run build"
  publish = ".vitepress/dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

### Node.js 服务器

```bash
npm install -g pm2
pm2 start server.js --name vitepress
```

### Docker

```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

EXPOSE 3000
CMD ["node", "server.js"]
```

## 关键点

- SSG 是默认的,SSR 需要自定义服务器
- 使用 `vite.ssrLoadModule()` 加载 SSR 入口
- `ClientOnly` 组件包装客户端特定代码
- 使用 `import.meta.env.SSR` 检测 SSR 环境
- 实现缓存以提高性能
- 预渲染静态路由以提高性能

<!--
Source references:
- https://vitepress.dev/guide/ssr
-->
