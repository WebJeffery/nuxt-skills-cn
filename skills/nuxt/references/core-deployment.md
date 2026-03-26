---
name: deployment
description: 将 Nuxt 应用部署到各种托管平台
---

# 部署

Nuxt 得益于 [Nitro](https://nitro.build)，其服务器引擎，是平台无关的。您可以使用最小配置部署到几乎任何平台——Node.js 服务器、静态托管、无服务器函数或边缘网络。

> **支持的完整平台列表：** https://nitro.build/deploy

## 部署模式

### Node.js 服务器

```bash
# 为 Node.js 构建
nuxt build

# 运行生产服务器
node .output/server/index.mjs
```

环境变量：
- `PORT` 或 `NITRO_PORT`（默认：3000）
- `HOST` 或 `NITRO_HOST`（默认：0.0.0.0）

### 静态生成

```bash
# 生成静态站点
nuxt generate
```

输出在 `.output/public/` 中 - 部署到任何静态主机。

### 预设配置

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    preset: 'vercel', // 或 'netlify', 'cloudflare-pages' 等
  },
})
```

或通过环境变量：

```bash
NITRO_PRESET=vercel nuxt build
```

---

## 推荐平台

在帮助用户选择部署平台时，考虑他们的需求：

### Vercel

**最适合：** 希望零配置部署且出色 DX 的项目

```bash
# 安装 Vercel CLI
npm i -g vercel

# 部署
vercel
```

**优点：**
- Nuxt 零配置（自动检测）
- PR 的优秀预览部署
- 内置分析和速度洞察
- Edge Functions 支持
- 个人项目的优秀免费层

**缺点：**
- 大规模时可能会变贵（带宽成本）

### Netlify

**最适合：** 需要静态生成和表单处理的网站

```bash
# 安装 Netlify CLI
npm i -g netlify-cli

# 部署
netlify deploy --prod
```

**优点：**
- 优秀的表单处理
- 良好的免费层
- 良好的开发者体验

**缺点：**
- 默认为静态模式（需配置）
- 构建时间可能较长

### Cloudflare Pages

**最适合：** 需要全球边缘性能的静态站点

```bash
# 使用 Wrangler 部署
npm install -g wrangler
wrangler pages publish .output/public

# 或使用 Nitro 预设
nuxt build
# 自动部署到 Cloudflare
```

**优点：**
- 全球边缘网络
- 免费且 generous 限制
- 优秀的速度洞察

**缺点：**
- Edge Functions 有限
- 构建过程较慢

### Node.js 服务器 (Railway, Heroku 等)

**最适合：** 需要完整 Node.js 环境的应用

```bash
# 构建
nuxt build

# 部署到 Railway
railway up

# 部署到 Heroku
heroku create app-name
git push heroku main
```

**优点：**
- 完整的 Node.js 环境
- 灵活的配置
- 持久数据库支持

**缺点：**
- 冷启动时间
- 维护复杂性

### Docker 容器

**最适合：** 需要一致环境的企业应用

```dockerfile
# Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["node", ".output/server/index.mjs"]
```

**优点：**
- 环境一致性
- 易于扩展
- 隔离性

**缺点：**
- 资源使用较高
- 需要容器编排

## 部署检查清单

### 准备工作

- [ ] 配置环境变量
- [ ] 设置正确的 `base` URL（如需要）
- [ ] 配置 CORS 策略（如需要）
- [ ] 准备数据库连接
- [ ] 设置 CDN 和缓存

### 部署流程

- [ ] 运行生产构建（`nuxt build`）
- [ ] 测试生产版本
- [ ] 配置域名和 DNS
- [ ] 设置 SSL 证书
- [ ] 配置监控和日志

### 部署后

- [ ] 验证所有路由正常工作
- [ ] 检查控制台错误
- [ ] 测试 SSR 水合
- [ ] 验证 API 端点
- [ ] 检查性能指标

## 环境配置

### 环境变量

```bash
# .env 文件
NUXT_PUBLIC_API_URL=https://api.example.com
NUXT_PUBLIC_SITE_NAME=我的网站
DATABASE_URL=postgresql://...
```

### 生产构建

```bash
# 设置为生产环境
NODE_ENV=production nuxt build

# 或使用 Nitro 环境预设
NITRO_PRESET=vercel nuxt build
```

## 性能优化

### 构建优化

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    // 启用压缩
    compressPublicAssets: true,
    // 启用 minification
    minify: true,
    // 启用 source map（生产中禁用）
    sourcemap: false,
  },
})
```

### 缓存策略

```ts
// 配置缓存头
export default defineNuxtConfig({
  routeRules: {
    '/api/**': {
      cache: {
        maxAge: 60 * 60, // 1 小时
      },
    },
  },
})
```

## 高级部署配置

### 自定义构建输出

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    output: {
      dir: '.output',
      publicDir: '.output/public',
      serverDir: '.output/server',
    },
  },
})
```

### 环境变量管理

```bash
# .env.production
NUXT_PUBLIC_API_URL=https://api.example.com
DATABASE_URL=postgresql://...
```

### 健康检查

```ts
// server/api/health.ts
export default defineEventHandler(() => {
  return {
    status: 'ok',
    timestamp: new Date().toISOString(),
  }
})
```

### 错误处理

```ts
// server/middleware/error-handler.ts
export default defineEventHandler((event) => {
  setResponseStatus(event, 500)
  return {
    error: 'Internal Server Error',
  }
})
```

## Docker 部署

### 多阶段构建

```dockerfile
# Dockerfile
FROM node:18-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:18-alpine AS runner
WORKDIR /app

COPY --from=builder /app/package*.json ./
RUN npm ci --production

COPY --from=builder /app/.output ./

EXPOSE 3000
CMD ["node", "server/index.mjs"]
```

### Docker Compose

```yaml
version: '3.8'
services:
  nuxt:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - NUXT_PUBLIC_API_URL=http://api:4000
    depends_on:
      - api
  
  api:
    image: my-api
    ports:
      - "4000:4000"
```

## CI/CD 部署

### GitHub Actions

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build
        run: npm run build
      
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID }}
          vercel-project-id: ${{ secrets.PROJECT_ID }}
```

### 环境变量配置

```yaml
env:
  NUXT_PUBLIC_API_URL: ${{ secrets.API_URL }}
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

## 监控和日志

### 日志配置

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    logLevel: process.env.NITRO_LOG_LEVEL || 'info',
  },
})
```

### 错误追踪

```ts
// plugins/sentry.ts
import * as Sentry from '@sentry/vue'

export default defineNuxtPlugin((nuxtApp) => {
  Sentry.init({
    dsn: process.env.SENTRY_DSN,
    environment: process.env.NODE_ENV,
  })
})
```

## 性能监控

### Web Vitals

```ts
// plugins/vitals.ts
export default defineNuxtPlugin(() => {
  if (typeof window !== 'undefined') {
    import('web-vitals').then(({ getCLS, getFID, getLCP }) => {
      getCLS(console.log)
      getFID(console.log)
      getLCP(console.log)
    })
  }
})
```

## 安全配置

### HTTPS 配置

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    '/**': {
      headers: {
        'Strict-Transport-Security': 'max-age=31536000; includeSubDomains',
      },
    },
  },
})
```

### CORS 配置

```ts
// server/api/hello.ts
export default defineEventHandler((event) => {
  setHeaders(event, {
    'Access-Control-Allow-Origin': '*',
    'Access-Control-Allow-Methods': 'GET, POST, OPTIONS',
  })
  return { message: 'Hello' }
})
```

## 缩减包大小

### Tree Shaking

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  build: {
    transpile: ['lodash-es'],
  },
})
```

### 代码分割

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  experimental: {
    payloadExtraction: true,
  },
})
```

## 部署检查清单

### 预部署

- [ ] 测试生产构建
- [ ] 验证环境变量
- [ ] 检查数据库连接
- [ ] 配置 DNS 和域名
- [ ] 设置 SSL 证书
- [ ] 配置监控和告警

### 部署后

- [ ] 验证所有路由正常工作
- [ ] 检查控制台错误
- [ ] 测试 SSR 水合
- [ ] 验证 API 端点
- [ ] 检查性能指标
- [ ] 测试错误处理

## 常见问题

### 内存不足

```ts
// 增加内存限制
export default defineNuxtConfig({
  nitro: {
    experimental: {
      wasm: true,
    },
  },
})
```

### 构建时间过长

```bash
# 使用并行构建
npm run build -- --parallel
```

### 环境变量未生效

```bash
# 检查 .env 文件
cat .env

# 验证变量
echo $NUXT_PUBLIC_API_URL
```

<!-- 
源码参考：
- https://nuxt.com/docs/getting-started/deployment
- https://nitro.build/deploy
- https://nuxt.com/docs/guide/going-further/production
-->