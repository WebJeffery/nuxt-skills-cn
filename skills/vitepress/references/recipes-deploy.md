---
name: vitepress-deployment
description: 将 VitePress 站点部署到各种平台,包括 GitHub Pages、Netlify、Vercel 等
---

# 部署

将 VitePress 静态站点部署到各种托管平台。

## 构建和预览

```bash
# 构建生产文件
npm run docs:build

# 本地预览
npm run docs:preview
```

默认输出在 `.vitepress/dist` 中。

## 设置基础路径

对于子路径部署(例如 `https://user.github.io/repo/`):

```ts
// .vitepress/config.ts
export default {
  base: '/repo/'
}
```

## GitHub Pages

创建 `.github/workflows/deploy.yml`:

```yaml
name: Deploy VitePress

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
        with:
          fetch-depth: 0
      - uses: actions/setup-node@v6
        with:
          node-version: 24
          cache: npm
      - run: npm ci
      - run: npm run docs:build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: docs/.vitepress/dist

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/deploy-pages@v4
        id: deployment
```

在仓库设置中启用 GitHub Pages → Pages → Source: "GitHub Actions"。

对于 pnpm,在 setup-node 之前添加:

```yaml
- uses: pnpm/action-setup@v4
  with:
    version: 9
```

## Netlify / Vercel / Cloudflare Pages

在仪表板中配置:

| 设置 | 值 |
|---------|-------|
| 构建命令 | `npm run docs:build` |
| 输出目录 | `docs/.vitepress/dist` |
| Node 版本 | `20` (或更高) |

**警告:** 不要为 HTML 启用"自动最小化" - 它会删除 Vue 水合注释。

### Vercel 配置

为了干净的 URL,添加 `vercel.json`:

```json
{
  "cleanUrls": true
}
```

## GitLab Pages

创建 `.gitlab-ci.yml`:

```yaml
image: node:18

pages:
  cache:
    paths:
      - node_modules/
  script:
    - npm install
    - npm run docs:build
  artifacts:
    paths:
      - public
  only:
    - main
```

如果需要,在配置中设置 `outDir: '../public'`。

## Firebase

```json
// firebase.json
{
  "hosting": {
    "public": "docs/.vitepress/dist",
    "ignore": []
  }
}
```

```bash
npm run docs:build
firebase deploy
```

## Nginx

```nginx
server {
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml;

    listen 80;
    server_name _;
    index index.html;

    location / {
        root /app;
        try_files $uri $uri.html $uri/ =404;
        error_page 404 /404.html;
        error_page 403 /404.html;
    }

    # 缓存哈希资产
    location ~* ^/assets/ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

**重要:** 不要像 SPA 那样默认为 `index.html` - 使用 `$uri.html` 以获得干净的 URL。

## HTTP 缓存头

对于哈希资产(不可变):

```
Cache-Control: max-age=31536000, immutable
```

### Netlify `_headers`

放置在 `docs/public/_headers` 中:

```
/assets/*
  cache-control: max-age=31536000
  cache-control: immutable
```

### Vercel `vercel.json`

```json
{
  "headers": [
    {
      "source": "/assets/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "max-age=31536000, immutable"
        }
      ]
    }
  ]
}
```

## 其他平台

| 平台 | 指南 |
|----------|-------|
| Azure Static Web Apps | 设置 `app_location: /`, `output_location: docs/.vitepress/dist` |
| Surge | `npx surge docs/.vitepress/dist` |
| Heroku | 使用 `heroku-buildpack-static` |
| Render | 构建: `npm run docs:build`, 发布: `docs/.vitepress/dist` |
| Kinsta | 遵循 [Kinsta 文档](https://kinsta.com/docs/vitepress-static-site-example/) |

## 关键点

- 为子路径部署设置 `base`
- GitHub Pages 需要工作流文件并在设置中启用 Pages
- 大多数平台: 构建 `npm run docs:build`, 输出 `docs/.vitepress/dist`
- 不要启用 HTML 最小化(破坏水合)
- 使用不可变头缓存 `/assets/*`
- 对于 Nginx 上的干净 URL,使用 `try_files $uri $uri.html $uri/ =404`

<!--
Source references:
- https://vitepress.dev/guide/deploy
-->
