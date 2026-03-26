---
name: server-routes
description: Nuxt 中的 API 路由、服务器中间件和 Nitro 服务器引擎
---

# 服务器路由

Nuxt 包含 Nitro 服务器引擎，用于构建具有 API 路由和服务器中间件的全栈应用程序。

## API 路由

在 `server/api/` 目录中创建文件：

```ts
// server/api/hello.ts
export default defineEventHandler((event) => {
  return { message: '你好世界' }
})
```

在 `/api/hello` 访问。

### HTTP 方法

```ts
// server/api/users.get.ts - GET /api/users
export default defineEventHandler(() => {
  return getUsers()
})

// server/api/users.post.ts - POST /api/users
export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  return createUser(body)
})

// server/api/users/[id].put.ts - PUT /api/users/:id
export default defineEventHandler(async (event) => {
  const id = getRouterParam(event, 'id')
  const body = await readBody(event)

  return updateUser(id, body)
})
```

## 请求处理

### 读取请求体

```ts
// 使用 readBody 读取请求体
export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  // body 是根据 Content-Type 自动解析
  return processUser(body)
})

// 读取原始文本
export default defineEventHandler(async (event) => {
  const rawBody = await readRawBody(event)
  return processRawData(rawBody)
})
```

### 请求参数

```ts
// 从 URL 获取参数
export default defineEventHandler((event) => {
  const query = getQuery(event)
  const page = Number(query.page) || 1
  return getUsers(page)
})
```

### 响应辅助工具

```ts
import { z } from 'zod'

// 定义响应模式
const userSchema = z.object({
  id: z.string(),
  name: z.string(),
  email: z.string().email()
})

export default defineEventHandler(async (event) => {
  const body = await readValidatedBody(event, userSchema)
  return body
})
```

## 服务器中间件

### 创建中间件

```ts
// middleware/auth.ts
export default defineEventHandler((event) => {
  // 从请求头获取令牌
  const token = getCookie(event, 'auth-token')
  
  if (!token) {
    throw createError({
      statusCode: 401,
      statusMessage: '未授权'
    })
  }
  
  // 将用户信息添加到请求上下文
  event.context.user = { id: '123', name: '用户' }
})
```

### 使用中间件

```ts
// server/api/users.get.ts
export default defineEventHandler(async (event) => {
  // 中间件已经在事件上下文中设置用户信息
  const user = event.context.user
  
  if (!user) {
    throw createError({
      statusCode: 401,
      statusMessage: '需要登录'
    })
  }
  
  return getUsers()
})
```

## 错误处理

### 创建错误响应

```ts
import { createError } from 'h3'

export default defineEventHandler((event) => {
  throw createError({
    statusCode: 404,
    statusMessage: '用户不存在'
  })
})
```

### 自定义错误响应

```ts
export default defineEventHandler((event) => {
  throw createError({
    statusCode: 400,
    statusMessage: '请求数据无效',
    data: { errorCode: 'INVALID_INPUT' }
  })
})
```

## CORS 配置

### 启用 CORS

```ts
// server/api/public.ts
export default defineEventHandler((event) => {
  setHeader(event, 'Access-Control-Allow-Origin', '*')
  setHeader(event, 'Access-Control-Allow-Methods', 'GET,POST,PUT,DELETE,OPTIONS')
  setHeader(event, 'Access-Control-Allow-Headers', 'Content-Type,Authorization')
  
  return { message: 'CORS 已启用' }
})
```

### 预检请求

```ts
// server/api/users.post.ts
export default defineEventHandler(async (event) => {
  if (getMethod(event) === 'OPTIONS') {
    setHeader(event, 'Access-Control-Allow-Origin', '*')
    setHeader(event, 'Access-Control-Allow-Methods', 'POST')
    setHeader(event, 'Access-Control-Allow-Headers', 'Content-Type')
    return { status: 204 }
  }
  
  const body = await readBody(event)
  return createUser(body)
})
```

## 数据验证

### 使用 Zod 验证

```ts
import { z } from 'zod'

const userSchema = z.object({
  name: z.string().min(2),
  email: z.string().email()
})

export default defineEventHandler(async (event) => {
  try {
    const body = await readValidatedBody(event, userSchema)
    return createUser(body)
  } catch (error) {
    throw createError({
      statusCode: 400,
      statusMessage: '验证失败',
      data: error.issues
    })
  }
})
```

## 文件上传

### 处理文件上传

```ts
// server/api/upload.ts
export default defineEventHandler(async (event) => {
  const formData = await readFormData(event)
  const file = formData.get('file')
  
  if (!file) {
    throw createError({
      statusCode: 400,
      statusMessage: '未提供文件'
    })
  }
  
  // 处理文件...
  const uploaded = await saveFile(file)
  
  return { filename: uploaded.name, size: uploaded.size }
})
```

## 数据库集成

### 连接数据库

```ts
// server/api/db.ts
import { drizzle } from 'drizzle-orm'
import { neon } from '@neondatabase/neon'

export default defineEventHandler(async (event) => {
  const db = neon(drizzleConfig)
  const users = await db.select().from(users).all()
  return users
})
```

## 性能优化

### 响应缓存

```ts
import { defineCachedEventHandler } from 'h3'

// 缓存响应 10 分钟
export default defineCachedEventHandler({
  maxAge: 600,
  handler: async (event) => {
    const users = await getUsers()
    return users
  }
})
```

### 压缩响应

```ts
import { defineCachedEventHandler } from 'h3'

export default defineCachedEventHandler({
  headers: { 'Content-Encoding': 'gzip' },
  handler: async (event) => {
    const data = await getUsers()
    return data
  }
})
```

## 环境变量

### 访问配置

```ts
// server/api/config.ts
export default defineEventHandler((event) => {
  const config = useRuntimeConfig()
  
  return {
    apiUrl: config.apiBaseUrl,
    databaseUrl: config.databaseUrl
  }
})
```

## 安全最佳实践

### 输入验证

```ts
// 始终验证所有输入
export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  
  // 验证和净化输入
  const sanitized = sanitizeInput(body.name)
  
  return { name: sanitized }
})
```

### 速率限制

```ts
import { defineRateLimiter } from 'h3'

const limiter = defineRateLimiter({
  intervalMs: 60_000,
  requestsPerInterval: 100
})

export default defineEventHandler((event) => {
  const ip = getRequestIP(event)
  
  if (!limiter.check(ip)) {
    throw createError({
      statusCode: 429,
      statusMessage: '请求过多'
    })
  }
})
```

## 高级用法

### WebSocket 支持

```ts
// server/api/ws.ts
import { createServer } from 'http'
import { Server } from 'socket.io'

let io: Server

export default defineEventHandler(() => {
  if (!io) {
    const httpServer = createServer()
    io = new Server(httpServer)
    
    io.on('connection', (socket) => {
      console.log('客户端连接')
      
      socket.on('message', (data) => {
        socket.emit('message', data)
      })
    })
  }
  
  return { status: 'WebSocket server running' }
})
```

### 流式响应

```ts
// server/api/stream.ts
export default defineEventHandler(async (event) => {
  setResponseHeaders(event, {
    'Content-Type': 'text/event-stream',
    'Cache-Control': 'no-cache',
  })
  
  const stream = new ReadableStream({
    async start(controller) {
      for (let i = 0; i < 10; i++) {
        controller.enqueue(`data: ${i}\n\n`)
        await new Promise(resolve => setTimeout(resolve, 1000))
      }
      controller.close()
    },
  })
  
  return stream
})
```

### 后台任务

```ts
// server/api/task.ts
import { setTask } from 'nitropack/runtime'

export default defineEventHandler(async (event) => {
  const taskId = setTask(async () => {
    await processLargeData()
  })
  
  return { taskId }
})
```

### 文件处理

```ts
// server/api/upload.ts
export default defineEventHandler(async (event) => {
  const form = await readFormData(event)
  const file = form.get('file') as File
  
  if (!file) {
    throw createError({ statusCode: 400, message: 'No file' })
  }
  
  const bytes = await file.arrayBuffer()
  const buffer = Buffer.from(bytes)
  
  return { size: buffer.length, name: file.name }
})
```

## 测试

### 单元测试

```ts
// tests/api/hello.test.ts
import { describe, it, expect } from 'vitest'
import { $fetch, setup } from '@nuxt/test-utils/e2e'

await setup({ rootDir: './fixtures/basic' })

describe('API Routes', () => {
  it('returns hello message', async () => {
    const response = await $fetch('/api/hello')
    expect(response).toEqual({ message: 'Hello' })
  })
})
```

### 集成测试

```ts
// tests/integration/api.test.ts
import { describe, it, expect } from 'vitest'
import { $fetch } from '@nuxt/test-utils'

describe('API Integration', () => {
  it('creates user', async () => {
    const user = await $fetch('/api/users', {
      method: 'POST',
      body: { name: 'Test User' },
    })
    
    expect(user).toHaveProperty('id')
    expect(user.name).toBe('Test User')
  })
})
```

## 性能优化

### 数据库连接池

```ts
// server/utils/db.ts
import { Pool } from 'pg'

let pool: Pool

export function useDb() {
  if (!pool) {
    pool = new Pool({
      connectionString: process.env.DATABASE_URL,
      max: 20,
      idleTimeoutMillis: 30000,
    })
  }
  
  return pool
}
```

### 查询缓存

```ts
// server/api/cached.ts
import { defineCachedEventHandler } from 'h3'

export default defineCachedEventHandler({
  maxAge: 600,
  getKey: (event) => {
    return getQuery(event).id || 'default'
  },
  handler: async (event) => {
    return await fetchFromDb(getQuery(event).id)
  },
})
```

## 最佳实践

1. **验证所有输入** - 使用 Zod 或类似库
2. **错误处理** - 提供有意义的错误消息
3. **速率限制** - 防止滥用
4. **安全头** - 添加 CORS 和安全头
5. **缓存** - 使用适当缓存提高性能

## 常见问题

### CORS 错误

**解决：**

```ts
export default defineEventHandler((event) => {
  const origin = getHeader(event, 'origin')
  const allowedOrigins = ['https://example.com']
  
  if (origin && allowedOrigins.includes(origin)) {
    setHeader(event, 'Access-Control-Allow-Origin', origin)
  }
  
  return { message: 'OK' }
})
```

### 文件上传过大

**解决：**

```ts
export default defineEventHandler(async (event) => {
  const body = await readBody(event, { maxBodySize: 10 * 1024 * 1024 })
  return body
})
```

<!-- 
源码参考：
- https://nuxt.com/docs/getting-started/server
- https://nuxt.com/docs/guide/going-further/server
- https://nuxt.com/docs/api/utils/define-event-handler
-->
