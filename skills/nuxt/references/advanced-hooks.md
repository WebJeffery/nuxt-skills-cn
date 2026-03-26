---
name: lifecycle-hooks
description: Nuxt 和 Nitro 钩子，用于扩展构建时和运行时行为
---

# 生命周期钩子

Nuxt 提供钩子来接入构建过程、应用生命周期和服务器运行时。

## 构建时钩子 (Nuxt)

在 `nuxt.config.ts` 或模块中使用：

### 在 nuxt.config.ts 中

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  hooks: {
    'build:before': () => {
      console.log('构建开始...')
    },
    'pages:extend': (pages) => {
      // 添加自定义页面
      pages.push({
        name: 'custom',
        path: '/custom',
        file: '~/pages/custom.vue',
      })
    },
    'components:dirs': (dirs) => {
      // 添加组件目录
      dirs.push({ path: '~/extra-components' })
    },
  },
})
```

### 在模块中

```ts
// modules/my-module.ts
export default defineNuxtModule({
  setup(options, nuxt) {
    nuxt.hook('ready', async (nuxt) => {
      console.log('Nuxt 已就绪')
    })

    nuxt.hook('close', async (nuxt) => {
      console.log('Nuxt 正在关闭')
    })

    nuxt.hook('modules:done', () => {
      console.log('所有模块已加载')
    })
  },
})
```

### 常用构建钩子

| 钩子 | 触发时机 |
|------|------|
| `kit:compatibility` | 兼容性检查扩展 |
| `ready` | Nuxt 初始化完成 |
| `close` | Nuxt 正在关闭 |
| `modules:before` | 安装用户模块前 |
| `modules:done` | 所有模块已安装 |
| `app:resolve` | app 实例解析后 |
| `app:templates` | NuxtApp 生成时 |
| `app:templatesGenerated` | 模板编译到 VFS 后 |
| `build:before` | 构建开始前 |
| `build:done` | 构建完成 |
| `build:manifest` | Vite 和 webpack 构建 manifest 时 |
| `build:error` | 构建发生错误时 |
| `builder:generateApp` | 生成应用前 |
| `builder:watch` | 开发模式文件变化时 |
| `pages:extend` | 页面路由已解析 |
| `pages:resolved` | 页面路由已增强元数据 |
| `components:dirs` | 组件目录正在解析 |
| `components:extend` | 扩展新组件 |
| `imports:sources` | 扩展导入源 |
| `imports:extend` | 扩展自动导入 |
| `imports:dirs` | 扩展导入目录 |
| `nitro:config` | Nitro 配置最终确定前 |
| `nitro:init` | Nitro 初始化后 |
| `nitro:build:before` | 构建 Nitro 实例前 |
| `prepare:types` | 写入 TypeScript 配置文件前 |
| `vite:extend` | Vite 上下文已创建 |
| `vite:extendConfig` | Vite 配置最终确定前 |
| `vite:serverCreated` | Vite 服务器已创建 |
| `vite:compiled` | Vite 服务器已编译 |

## 应用钩子 (运行时)

在插件和组合式函数中使用。Nuxt 提供了丰富的运行时钩子来处理应用生命周期事件。

### 在插件中

```ts
// plugins/lifecycle.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.hook('app:created', (vueApp) => {
    console.log('Vue 应用已创建')
  })

  nuxtApp.hook('app:mounted', (vueApp) => {
    console.log('应用已挂载')
  })

  nuxtApp.hook('page:start', () => {
    console.log('页面导航开始')
  })

  nuxtApp.hook('page:finish', () => {
    console.log('页面导航完成')
  })

  nuxtApp.hook('page:loading:start', () => {
    console.log('页面加载开始')
  })

  nuxtApp.hook('page:loading:end', () => {
    console.log('页面加载结束')
  })
})
```

### 在对象语法插件中定义钩子

```ts
// plugins/my-plugin.ts
export default defineNuxtPlugin({
  name: 'my-plugin',
  enforce: 'pre', // 或 'post'
  async setup(nuxtApp) {
    // 插件逻辑
  },
  hooks: {
    // 直接注册 Nuxt app 运行时钩子
    'app:created': function() {
      const nuxtApp = useNuxtApp()
      // 在钩子中执行逻辑
    },
    'page:finish': function() {
      // 页面导航完成后的逻辑
    },
  },
})
```

### 常用应用钩子

| 钩子 | 参数 | 环境 | 描述 |
|------|------|------|------|
| `app:created` | `vueApp` | 服务器和客户端 | Vue 应用实例创建后调用 |
| `app:beforeMount` | `vueApp` | 仅客户端 | 挂载应用前调用 |
| `app:mounted` | `vueApp` | 仅客户端 | Vue 应用已挂载到浏览器 |
| `app:error` | `err` | 服务器和客户端 | 发生致命错误时 |
| `app:error:cleared` | `{ redirect? }` | 服务器和客户端 | 错误被清除时 |
| `app:rendered` | `renderContext` | 仅服务器 | SSR 渲染完成时 |
| `app:redirected` | - | 仅服务器 | SSR 重定向前 |
| `app:suspense:resolve` | `appComponent` | 仅客户端 | Suspense 解析事件 |
| `app:manifest:update` | `{ id, timestamp }` | 仅客户端 | 检测到应用新版本 |
| `app:data:refresh` | `keys?` | 仅客户端 | 调用 `refreshNuxtData` 时 |
| `vue:setup` | - | 服务器和客户端 | Nuxt 根初始化时（必须同步） |
| `vue:error` | `err, target, info` | 服务器和客户端 | Vue 错误传播到根组件 |
| `page:start` | `pageComponent?` | 仅客户端 | 页面 Suspense pending 事件 |
| `page:finish` | `pageComponent?` | 仅客户端 | 页面 Suspense 解析事件 |
| `page:loading:start` | - | 仅客户端 | 新页面 setup() 运行时 |
| `page:loading:end` | - | 仅客户端 | page:finish 之后 |
| `page:transition:finish` | `pageComponent?` | 仅客户端 | 页面过渡的 onAfterLeave 事件后 |
| `link:prefetch` | `to` | 仅客户端 | NuxtLink 链接被预取时 |
| `dev:ssr-logs` | `logs` | 仅客户端 | 服务器端日志传递到客户端时 |
| `page:view-transition:start` | `transition` | 仅客户端 | document.startViewTransition 调用后 |

### 钩子执行顺序（服务器端）

1. Server plugins 执行
2. App plugins 执行（含 `.server` 后缀插件）
3. `app:created` 钩子调用
4. Route validation
5. App middleware 执行
6. Page 和组件渲染
7. 数据获取（`useFetch`、`useAsyncData`）
8. `app:rendered` 钩子调用
9. HTML 生成

### 钩子执行顺序（客户端）

1. App plugins 执行（含 `.client` 后缀插件）
2. `app:created` 钩子调用
3. Route validation
4. App middleware 执行
5. `app:beforeMount` 钩子调用
6. Vue 应用挂载和水合
7. `app:mounted` 钩子调用
8. Vue 生命周期钩子执行（`onMounted`、`onBeforeMount` 等）

### 使用运行时钩子

```ts
// composables/usePageTracking.ts
export function usePageTracking() {
  const nuxtApp = useNuxtApp()

  nuxtApp.hook('page:finish', () => {
    trackPageView(useRoute().path)
  })
}
```

### SSR 相关钩子使用

```ts
// plugins/ssr-tracker.ts
export default defineNuxtPlugin((nuxtApp) => {
  if (import.meta.server) {
    // 服务器端钩子：SSR 渲染完成后捕获渲染状态
    nuxtApp.hook('app:rendered', (ctx) => {
      console.log('SSR 渲染完成:', ctx)
    })
  }

  if (import.meta.client) {
    // 客户端钩子：水合完成后
    nuxtApp.hook('app:mounted', () => {
      console.log('应用已水合完成')
    })
  }
})
```

### 错误处理钩子

```ts
// plugins/error-handler.ts
export default defineNuxtPlugin((nuxtApp) => {
  // 捕获全局错误
  nuxtApp.hook('app:error', (error) => {
    console.error('应用错误:', error)
    // 可以在这里记录错误到错误追踪服务
  })

  // 错误被清除时
  nuxtApp.hook('app:error:cleared', ({ redirect }) => {
    console.log('错误已清除', redirect)
  })

  // Vue 错误捕获
  nuxtApp.hook('vue:error', (err, target, info) => {
    console.error('Vue 错误:', err, '目标:', target, '信息:', info)
  })
})
```

## 服务器钩子 (Nitro)

在服务器插件中使用。Nitro 钩子用于处理服务器端的请求生命周期。

### 在服务器插件中

```ts
// server/plugins/hooks.ts
export default defineNitroPlugin((nitroApp) => {
  // 在发送前修改 HTML
  nitroApp.hooks.hook('render:html', (html, { event }) => {
    html.head.push('<meta name="custom" content="value">')
    html.bodyAppend.push('<script>console.log("injected")</script>')
  })

  // 修改响应
  nitroApp.hooks.hook('render:response', (response, { event }) => {
    console.log('发送响应:', response.statusCode)
  })

  // 请求前
  nitroApp.hooks.hook('request', (event) => {
    console.log('请求:', event.path)
  })

  // 响应后
  nitroApp.hooks.hook('afterResponse', (event) => {
    console.log('响应已发送')
  })
})
```

### 常用 Nitro 钩子

| 钩子 | 参数 | 描述 |
|------|------|------|
| `request` | `event` | 收到请求时 |
| `beforeResponse` | `event, { body }` | 发送响应前 |
| `afterResponse` | `event, { body }` | 响应已发送 |
| `render:html` | `html, { event }` | 构造 HTML 前 |
| `render:response` | `response, { event }` | 发送响应前 |
| `render:island` | `islandResponse, { event, islandContext }` | 构造 island HTML 前 |
| `error` | `error, { event? }` | 发生错误时 |
| `close` | - | Nitro 关闭时 |
| `dev:ssr-logs` | `{ path, logs }` | 请求周期结束时（带服务器端日志数组） |

### HTML 注入示例

```ts
// server/plugins/inject-html.ts
export default defineNitroPlugin((nitroApp) => {
  nitroApp.hooks.hook('render:html', (html, { event }) => {
    // 注入自定义 meta 标签
    html.head.push(`<meta name="generator" content="My Custom App">`)

    // 注入内联脚本
    html.head.push(`
      <script>
        window.__INITIAL_STATE__ = ${JSON.stringify({ theme: 'dark' })}
      </script>
    `)

    // 在 body 末尾追加内容
    html.bodyAppend.push(`
      <div id="custom-footer">
        Powered by Nuxt
      </div>
    `)
  })
})
```

### 请求/响应拦截示例

```ts
// server/plugins/request-logger.ts
export default defineNitroPlugin((nitroApp) => {
  // 请求日志
  nitroApp.hooks.hook('request', (event) => {
    console.log(`[${new Date().toISOString()}] ${event.method} ${event.path}`)
  })

  // 响应日志
  nitroApp.hooks.hook('afterResponse', (event, { body }) => {
    const duration = Date.now() - event.context.startTime
    console.log(`响应耗时: ${duration}ms`)
  })

  // 响应前修改
  nitroApp.hooks.hook('beforeResponse', (event, { body }) => {
    // 可以在发送前修改响应体
    if (event.path.startsWith('/api/')) {
      event.context.response._data = {
        ...body,
        timestamp: new Date().toISOString(),
      }
    }
  })
})
```

## 自定义钩子

### 定义自定义钩子类型

```ts
// types/hooks.d.ts
import type { HookResult } from '@nuxt/schema'

declare module '#app' {
  interface RuntimeNuxtHooks {
    'my-app:event': (data: MyEventData) => HookResult
  }
}

declare module '@nuxt/schema' {
  interface NuxtHooks {
    'my-module:init': () => HookResult
  }
}

declare module 'nitropack/types' {
  interface NitroRuntimeHooks {
    'my-server:event': (data: any) => void
  }
}
```

### 调用自定义钩子

```ts
// 在插件中
export default defineNuxtPlugin((nuxtApp) => {
  // 调用自定义钩子
  nuxtApp.callHook('my-app:event', { type: 'custom' })
})

// 在模块中
export default defineNuxtModule({
  setup(options, nuxt) {
    nuxt.callHook('my-module:init')
  },
})
```

## useRuntimeHook

在组件中运行时调用钩子：

```vue
<script setup lang="ts">
// 为运行时钩子注册回调
useRuntimeHook('app:error', (error) => {
  console.error('应用错误:', error)
})

// 页面导航跟踪
useRuntimeHook('page:finish', () => {
  console.log('页面:', useRoute().path)
})
</script>
```

## 最佳实践

### 1. 钩子注册时机

**✅ 推荐：在插件 setup 中注册钩子**

```ts
// plugins/analytics.ts
export default defineNuxtPlugin((nuxtApp) => {
  // ✅ 在插件中注册，确保只执行一次
  nuxtApp.hook('page:finish', () => {
    analytics.track('pageview')
  })
})
```

**⚠️ 避免：在组件或 composables 中直接注册**

```ts
// composables/useAnalytics.ts
export function useAnalytics() {
  // ⚠️ 可能在多个地方调用，导致重复注册
  nuxtApp.hook('page:finish', () => {
    analytics.track('pageview')
  })
}
```

**正确做法：使用 useRuntimeHook**

```ts
// composables/useAnalytics.ts
export function useAnalytics() {
  // ✅ 使用 useRuntimeHook 确保正确性
  useRuntimeHook('page:finish', () => {
    analytics.track('pageview')
  })
}
```

### 2. 环境检查

始终使用环境检查确保代码在正确的环境中运行：

```ts
export default defineNuxtPlugin((nuxtApp) => {
  if (import.meta.server) {
    // 服务器端代码
    nuxtApp.hook('app:rendered', (ctx) => {
      // SSR 逻辑
    })
  }

  if (import.meta.client) {
    // 客户端代码
    nuxtApp.hook('app:mounted', () => {
      // 客户端逻辑
    })
  }
})
```

### 3. 避免重复注册

使用插件名称和 `dependsOn` 确保钩子只注册一次：

```ts
// plugins/01-base-plugin.ts
export default defineNuxtPlugin({
  name: 'base-plugin',
  setup(nuxtApp) {
    nuxtApp.provide('service', new MyService())
  },
})

// plugins/02-dependent-plugin.ts
export default defineNuxtPlugin({
  name: 'dependent-plugin',
  dependsOn: ['base-plugin'], // 依赖 base-plugin
  setup(nuxtApp) {
    nuxtApp.hook('app:created', () => {
      // 确保 service 已被提供
      const service = nuxtApp.$service
    })
  },
})
```

### 4. 清理钩子

虽然大多数钩子不需要手动清理，但如果在可销毁的组件中注册了钩子，应该使用 `onUnmounted` 清理：

```vue
<script setup lang="ts">
import { onUnmounted } from 'vue'

let removeHook: (() => void) | undefined

onMounted(() => {
  const nuxtApp = useNuxtApp()
  removeHook = nuxtApp.hook('page:start', () => {
    // 页面开始逻辑
  })
})

onUnmounted(() => {
  // 清理钩子
  removeHook?.()
})
</script>
```

### 5. SSR 水合注意事项

避免在 SSR 和客户端之间产生不一致：

```ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.hook('app:created', () => {
    // ✅ 在两端都执行的安全操作
    console.log('App created')
  })

  nuxtApp.hook('app:mounted', () => {
    // ✅ 仅客户端执行（安全）
    console.log('App mounted on client')
  })

  nuxtApp.hook('app:rendered', (ctx) => {
    // ✅ 仅服务器执行
    if (import.meta.server) {
      console.log('SSR rendered:', ctx)
    }
  })
})
```

### 6. 错误处理最佳实践

```ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.hook('app:error', (error) => {
    // 记录错误但不中断应用
    console.error('应用错误:', error)

    // 可以在这里发送错误到错误追踪服务
    if (import.meta.client) {
      // 客户端错误处理
      errorTrackingService.track(error)
    }
  })

  nuxtApp.hook('vue:error', (err, target, info) => {
    // Vue 组件错误处理
    console.error('Vue 组件错误:', err, info)

    // 可以决定是否阻止错误向上传播
    // 返回 false 可以阻止
  })
})
```

## 钩子示例

### 页面浏览跟踪

```ts
// plugins/analytics.client.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.hook('page:finish', () => {
    const route = useRoute()
    analytics.track('pageview', {
      path: route.path,
      title: document.title,
    })
  })
})
```

### 性能监控

```ts
// plugins/performance.client.ts
export default defineNuxtPlugin((nuxtApp) => {
  let navigationStart: number

  nuxtApp.hook('page:start', () => {
    navigationStart = performance.now()
  })

  nuxtApp.hook('page:finish', () => {
    const duration = performance.now() - navigationStart
    console.log(`导航耗时 ${duration}ms`)

    // 发送到性能监控服务
    performanceService.track({
      type: 'navigation',
      path: useRoute().path,
      duration,
    })
  })
})
```

### SSR 状态传递

```ts
// plugins/ssr-state.ts
export default defineNuxtPlugin((nuxtApp) => {
  if (import.meta.server) {
    // SSR 渲染完成后保存状态到 payload
    nuxtApp.hook('app:rendered', (ctx) => {
      nuxtApp.payload.ssrData = {
        timestamp: Date.now(),
        userAgent: useRequestHeaders()['user-agent'],
      }
    })
  }

  if (import.meta.client) {
    // 客户端水合后使用 SSR 数据
    nuxtApp.hook('app:mounted', () => {
      if (nuxtApp.payload.ssrData) {
        console.log('SSR 时间:', nuxtApp.payload.ssrData.timestamp)
      }
    })
  }
})
```

### 页面加载状态管理

```ts
// plugins/loading-indicator.client.ts
export default defineNuxtPlugin((nuxtApp) => {
  // 添加加载状态
  nuxtApp.provide('loading', ref(false))

  nuxtApp.hook('page:loading:start', () => {
    nuxtApp.$loading.value = true
  })

  nuxtApp.hook('page:loading:end', () => {
    nuxtApp.$loading.value = false
  })
})
```

### 页面过渡动画同步

```ts
// plugins/page-transition.client.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.hook('page:start', () => {
    document.body.classList.add('page-transitioning')
  })

  nuxtApp.hook('page:transition:finish', () => {
    document.body.classList.remove('page-transitioning')
  })
})
```

### 数据刷新监听

```ts
// plugins/data-refresh.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.hook('app:data:refresh', (keys) => {
    console.log('数据已刷新:', keys)

    // 可以在这里触发 UI 更新或通知
    if (keys?.includes('user')) {
      // 用户数据已刷新
    }
  })
})
```

### 链接预取跟踪

```ts
// plugins/link-prefetch.client.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.hook('link:prefetch', (to) => {
    console.log('预取页面:', to)
    // 可以在这里记录预取行为或优化预取策略
  })
})
```

### 应用版本更新检测

```ts
// plugins/version-update.client.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.hook('app:manifest:update', ({ id, timestamp }) => {
    console.log('检测到新版本:', id, timestamp)

    // 提示用户刷新页面
    const notification = useNotification()
    notification.show('应用有新版本可用', {
      action: () => window.location.reload(),
      actionText: '刷新',
    })
  })
})
```

### 开发环境日志增强

```ts
// plugins/dev-logs.client.ts
export default defineNuxtPlugin((nuxtApp) => {
  if (import.meta.dev) {
    nuxtApp.hook('dev:ssr-logs', (logs) => {
      // 在控制台显示服务器端日志
      console.group('Server Logs')
      logs.forEach(log => {
        console.log(`[${log.tag}]`, log.message, log.args)
      })
      console.groupEnd()
    })
  }
})
```

### View Transition API 集成

```ts
// plugins/view-transition.client.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.hook('page:view-transition:start', (transition) => {
    console.log('View transition 开始:', transition)

    // 可以在这里添加自定义过渡效果
    transition.updateCallbackDone?.then(() => {
      console.log('DOM 更新完成')
    })
  })
})
```

### 请求日志中间件

```ts
// server/plugins/request-logger.ts
export default defineNitroPlugin((nitroApp) => {
  nitroApp.hooks.hook('request', (event) => {
    event.context.startTime = Date.now()
    console.log(`[${event.method}] ${event.path}`)
  })

  nitroApp.hooks.hook('afterResponse', (event) => {
    const duration = Date.now() - event.context.startTime
    console.log(`[${event.method}] ${event.path} - ${duration}ms`)
  })
})
```

### HTML 优化和注入

```ts
// server/plugins/html-optimizer.ts
export default defineNitroPlugin((nitroApp) => {
  nitroApp.hooks.hook('render:html', (html, { event }) => {
    // 移除不必要的空白
    html.head = html.head.map(tag => tag.trim())

    // 添加性能标记
    html.head.push(`
      <script>
        performance.mark('nuxt:html:start')
      </script>
    `)

    // 添加 CSP 头部
    html.head.push(`
      <meta http-equiv="Content-Security-Policy" content="
        default-src 'self';
        script-src 'self' 'unsafe-inline' 'unsafe-eval';
        style-src 'self' 'unsafe-inline';
      ">
    `)
  })
})
```

### 错误页面增强

```ts
// server/plugins/error-handler.ts
export default defineNitroPlugin((nitroApp) => {
  nitroApp.hooks.hook('error', (error, { event }) => {
    // 记录错误详情
    console.error('服务器错误:', {
      error: error.message,
      stack: error.stack,
      path: event?.path,
      method: event?.method,
    })

    // 发送到错误追踪服务
    if (errorTrackingService) {
      errorTrackingService.capture(error, {
        path: event?.path,
        method: event?.method,
      })
    }
  })
})
```

### 响应数据转换

```ts
// server/plugins/response-transformer.ts
export default defineNitroPlugin((nitroApp) => {
  nitroApp.hooks.hook('render:response', (response, { event }) => {
    // 为 API 响应添加标准格式
    if (event?.path.startsWith('/api/')) {
      response._data = {
        success: true,
        data: response._data,
        timestamp: new Date().toISOString(),
      }
    }
  })
})
```

## 高级用法

### 模块中使用 Nitro 钩子

```ts
// modules/my-module.ts
export default defineNuxtModule({
  setup(options, nuxt) {
    // 在 Nitro 初始化后注册服务器钩子
    nuxt.hook('nitro:init', (nitro) => {
      nitro.hooks.hook('render:html', (html, { event }) => {
        html.head.push(`<meta name="module" content="my-module">`)
      })
    })
  },
})
```

### 条件钩子注册

```ts
export default defineNuxtPlugin((nuxtApp) => {
  // 根据环境或条件注册钩子
  if (nuxtApp.$config.public.enableAnalytics) {
    nuxtApp.hook('page:finish', () => {
      analytics.track('pageview')
    })
  }

  if (import.meta.dev) {
    nuxtApp.hook('app:error', (error) => {
      console.error('开发环境错误详情:', error)
    })
  }
})
```

### 钩子链式调用

```ts
export default defineNuxtPlugin((nuxtApp) => {
  // 多个钩子可以链式工作
  nuxtApp.hook('page:start', () => {
    console.log('页面开始加载')
  })

  nuxtApp.hook('page:loading:start', () => {
    console.log('显示加载指示器')
  })

  nuxtApp.hook('page:finish', () => {
    console.log('页面数据加载完成')
  })

  nuxtApp.hook('page:loading:end', () => {
    console.log('隐藏加载指示器')
  })

  nuxtApp.hook('page:transition:finish', () => {
    console.log('页面过渡完成')
  })
})
```

### 异步钩子处理

```ts
export default defineNuxtPlugin((nuxtApp) => {
  // 钩子可以是异步的
  nuxtApp.hook('app:created', async () => {
    await initializeService()
    await loadConfiguration()
  })

  // 错误处理
  nuxtApp.hook('page:finish', async () => {
    try {
      await sendAnalyticsData()
    } catch (error) {
      console.error('Analytics 失败:', error)
    }
  })
})
```

### 带优先级的钩子（使用 enforce）

```ts
// plugins/01-pre-analytics.ts
export default defineNuxtPlugin({
  name: 'pre-analytics',
  enforce: 'pre', // 尽早执行
  setup(nuxtApp) {
    nuxtApp.hook('app:created', () => {
      console.log('预分析初始化')
    })
  },
})

// plugins/02-post-analytics.ts
export default defineNuxtPlugin({
  name: 'post-analytics',
  enforce: 'post', // 稍后执行
  setup(nuxtApp) {
    nuxtApp.hook('app:created', () => {
      console.log('后分析初始化')
    })
  },
})
```

## 调试技巧

### 钩子调试日志

```ts
// plugins/hook-debugger.ts
export default defineNuxtPlugin((nuxtApp) => {
  if (import.meta.dev) {
    const hookLog: Record<string, number[]> = {}

    // 包装所有钩子调用以记录执行
    const originalHook = nuxtApp.hook.bind(nuxtApp)
    nuxtApp.hook = (name, fn) => {
      const wrapped = (...args: any[]) => {
        const start = Date.now()
        console.log(`[Hook] ${name} called`, args)
        const result = fn(...args)
        const duration = Date.now() - start

        if (!hookLog[name]) hookLog[name] = []
        hookLog[name].push(duration)

        if (duration > 100) {
          console.warn(`[Hook] ${name} took ${duration}ms`)
        }

        return result
      }
      return originalHook(name, wrapped)
    }
  }
})
```

### 钩子执行追踪

```ts
export default defineNuxtPlugin((nuxtApp) => {
  if (import.meta.dev) {
    const trace = () => {
      const stack = new Error('Hook trace').stack || ''
      console.log('钩子调用堆栈:', stack.split('\n').slice(2, 5).join('\n'))
    }

    nuxtApp.hook('page:start', () => trace())
    nuxtApp.hook('page:finish', () => trace())
  }
})
```

### 性能分析

```ts
// plugins/hook-performance.ts
export default defineNuxtPlugin((nuxtApp) => {
  if (import.meta.dev) {
    const timings = new Map<string, number[]>()

    nuxtApp.hook('page:start', () => {
      timings.set('navigation', [Date.now()])
    })

    nuxtApp.hook('page:finish', () => {
      const navTime = Date.now() - (timings.get('navigation')?.[0] || 0)
      console.log(`页面导航耗时: ${navTime}ms`)
    })
  }
})
```

## 常见问题和解决方案

### Q: 为什么我的钩子没有被执行？

**A: 检查以下几点：**
1. 确保插件在正确的环境中执行（使用 `.client` 或 `.server` 后缀）
2. 检查插件名称和依赖关系
3. 确保没有在多个地方重复注册导致覆盖

```ts
// ❌ 错误：在多个文件中注册同名钩子
// plugins/a.ts
export default defineNuxtPlugin({
  name: 'plugin-a',
  setup: (n) => { n.hook('page:start', fn1) }
})
// plugins/b.ts
export default defineNuxtPlugin({
  name: 'plugin-b',
  setup: (n) => { n.hook('page:start', fn2) }
})

// ✅ 正确：使用不同的钩子或组合逻辑
// plugins/a.ts
export default defineNuxtPlugin({
  name: 'plugin-a',
  setup: (n) => { n.hook('page:start', fn1) }
})
// plugins/b.ts
export default defineNuxtPlugin({
  name: 'plugin-b',
  dependsOn: ['plugin-a'],
  setup: (n) => { n.hook('page:finish', fn2) }
})
```

### Q: 如何确保钩子只执行一次？

**A: 使用插件名称和环境检查：**

```ts
export default defineNuxtPlugin({
  name: 'unique-analytics',
  setup(nuxtApp) {
    if (import.meta.client) {
      nuxtApp.hook('page:finish', () => {
        // 这个钩子只会注册一次
        analytics.track('pageview')
      })
    }
  },
})
```

### Q: 钩子执行顺序如何控制？

**A: 使用文件名前缀、`enforce` 选项和 `dependsOn`：**

```ts
// 01-base.ts
export default defineNuxtPlugin({ name: 'base', setup: (n) => { /* ... */ } })

// 02-dependent.ts
export default defineNuxtPlugin({
  name: 'dependent',
  dependsOn: ['base'], // 确保 base 已执行
  setup: (n) => { /* ... */ }
})
```

## 参考资源

- [Nuxt Hooks API 文档](https://nuxt.com/docs/api/advanced/hooks)
- [Nuxt 生命周期](https://nuxt.com/docs/guide/concepts/nuxt-lifecycle)
- [Nitro Plugins 和 Hooks](https://nitro.build/guide/plugins)
- [unjs/hookable](https://github.com/unjs/hookable)

<!--
Source references:
- https://nuxt.com/docs/guide/going-further/hooks
- https://nuxt.com/docs/api/advanced/hooks
-->
