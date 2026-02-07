---
name: vite-security
description: Vite 安全最佳实践
---

# 安全最佳实践

## 环境变量安全

### 敏感信息保护

```bash
# .env - 不提交到版本控制
VITE_API_URL=https://api.example.com
VITE_APP_TITLE=My App

# .env.local - 本地开发专用，gitignore
VITE_API_KEY=your-secret-key
VITE_DB_PASSWORD=your-password

# .env.production - 生产环境配置
VITE_API_URL=https://api.example.com
```

### .gitignore 配置

```
# 环境变量文件
.env
.env.local
.env.*.local

# 敏感配置
*.key
*.pem
secrets/
```

### 服务端环境变量

```ts
// 服务端环境变量不会暴露给客户端
// 使用服务端 API 代理敏感请求
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: process.env.API_URL, // 服务端变量
        changeOrigin: true,
      },
    },
  },
})
```

## 内容安全策略

### CSP 配置

```ts
export default defineConfig({
  preview: {
    headers: {
      'Content-Security-Policy': "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:;",
    },
  },
})
```

### CSP 插件

```ts
import { defineConfig } from 'vite'
import csp from 'vite-plugin-csp'

export default defineConfig({
  plugins: [
    csp({
      policy: {
        'default-src': ["'self'"],
        'script-src': ["'self'", "'unsafe-inline'"],
        'style-src': ["'self'", "'unsafe-inline'"],
      },
    }),
  ],
})
```

## HTTPS 配置

### 开发环境 HTTPS

```ts
import fs from 'node:fs'

export default defineConfig({
  server: {
    https: {
      key: fs.readFileSync('path/to/key.pem'),
      cert: fs.readFileSync('path/to/cert.pem'),
    },
  },
})
```

### 生成自签名证书

```bash
# 使用 mkcert
mkcert -install
mkcert localhost 127.0.0.1

# 或使用 openssl
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes
```

### HSTS 配置

```ts
export default defineConfig({
  preview: {
    headers: {
      'Strict-Transport-Security': 'max-age=31536000; includeSubDomains',
    },
  },
})
```

## 依赖安全

### 依赖审计

```bash
# 使用 npm audit
npm audit

# 使用 pnpm audit
pnpm audit

# 使用 Snyk
npx snyk test

# 使用 npm-check-updates
npx npm-check-updates -u
```

### 锁定依赖版本

```json
{
  "dependencies": {
    "vue": "^3.4.0",
    "axios": "1.6.0"
  }
}
```

### 使用 pnpm overrides

```json
{
  "pnpm": {
    "overrides": {
      "vulnerable-package": "1.2.3"
    }
  }
}
```

## 输入验证

### 客户端验证

```ts
import { z } from 'zod'

const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
})

function validateInput(data: unknown) {
  return schema.parse(data)
}
```

### 服务端验证

```ts
// 始终在服务端验证
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        configure: (proxy, options) => {
          proxy.on('proxyReq', (proxyReq, req, res) => {
            // 验证请求
            if (!isValidRequest(req)) {
              res.status(400).end('Invalid request')
            }
          })
        },
      },
    },
  },
})
```

## XSS 防护

### 输出转义

```ts
// 使用框架内置的转义
// Vue 自动转义
<template>
  <div>{{ userInput }}</div>
</template>

// React 自动转义
function App({ content }) {
  return <div>{content}</div>
}
```

### DOMPurify 集成

```ts
import DOMPurify from 'dompurify'

function sanitizeHTML(html: string): string {
  return DOMPurify.sanitize(html)
}
```

### CSP 策略

```ts
export default defineConfig({
  preview: {
    headers: {
      'Content-Security-Policy': "default-src 'self'; script-src 'self'; object-src 'none';",
    },
  },
})
```

## CSRF 防护

### SameSite Cookie

```ts
export default defineConfig({
  preview: {
    headers: {
      'Set-Cookie': 'sessionId=xxx; Path=/; Secure; HttpOnly; SameSite=Strict',
    },
  },
})
```

### CSRF Token

```ts
// 生成 CSRF token
function generateCSRFToken(): string {
  return crypto.randomUUID()
}

// 验证 CSRF token
function validateCSRFToken(token: string, sessionToken: string): boolean {
  return token === sessionToken
}
```

## 认证和授权

### JWT 处理

```ts
// 在服务端处理 JWT
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        configure: (proxy, options) => {
          proxy.on('proxyReq', (proxyReq, req, res) => {
            const token = req.headers.authorization
            if (!isValidJWT(token)) {
              res.status(401).end('Unauthorized')
            }
          })
        },
      },
    },
  },
})
```

### OAuth 配置

```ts
export default defineConfig({
  server: {
    proxy: {
      '/auth': {
        target: 'https://auth.example.com',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/auth/, ''),
      },
    },
  },
})
```

## 数据传输安全

### HTTPS 强制

```ts
export default defineConfig({
  server: {
    https: true, // 开发环境使用 HTTPS
  },
  preview: {
    https: true, // 预览环境使用 HTTPS
  },
})
```

### 加密传输

```ts
import crypto from 'node:crypto'

function encryptData(data: string, key: string): string {
  const algorithm = 'aes-256-cbc'
  const iv = crypto.randomBytes(16)
  const cipher = crypto.createCipheriv(algorithm, key, iv)
  let encrypted = cipher.update(data, 'utf8', 'hex')
  encrypted += cipher.final('hex')
  return iv.toString('hex') + ':' + encrypted
}
```

## 日志和监控

### 安全日志

```ts
function logSecurityEvent(event: string, details: unknown) {
  console.error('[SECURITY]', event, details)
  
  // 发送到监控系统
  sendToMonitoring({ type: 'security', event, details })
}
```

### 错误处理

```ts
window.addEventListener('error', (event) => {
  logSecurityEvent('client-error', {
    message: event.message,
    filename: event.filename,
    lineno: event.lineno,
  })
})
```

## 文件上传安全

### 文件类型验证

```ts
function validateFileType(file: File): boolean {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif']
  return allowedTypes.includes(file.type)
}

function validateFileSize(file: File): boolean {
  const maxSize = 5 * 1024 * 1024 // 5MB
  return file.size <= maxSize
}
```

### 文件名清理

```ts
function sanitizeFileName(filename: string): string {
  return filename
    .replace(/[^a-zA-Z0-9._-]/g, '_')
    .replace(/\.{2,}/g, '.')
    .substring(0, 255)
}
```

## 最佳实践总结

1. **环境变量**：使用 .env.local 存储敏感信息，不提交到版本控制
2. **HTTPS**：开发和生产环境都使用 HTTPS
3. **CSP**：配置内容安全策略防止 XSS
4. **依赖安全**：定期审计依赖，及时更新漏洞
5. **输入验证**：客户端和服务端都进行输入验证
6. **XSS 防护**：使用框架内置转义和 DOMPurify
7. **CSRF 防护**：使用 SameSite Cookie 和 CSRF Token
8. **日志监控**：记录安全事件，及时响应

<!--
Source references:
- https://vite.dev/guide/build
- https://developer.mozilla.org/en-US/docs/Web/Security
-->
