---
name: vite-debugging
description: Vite 调试技巧和工具
---

# 调试技巧

## 开发服务器调试

### 启用调试日志

```ts
export default defineConfig({
  logLevel: 'info', // 'info' | 'warn' | 'error' | 'silent'
  clearScreen: false, // 保留终端输出
})
```

### 环境变量调试

```ts
export default defineConfig(({ mode }) => {
  console.log('当前模式:', mode)
  console.log('环境变量:', import.meta.env)

  return {
    // 配置
  }
})
```

## 源映射调试

### 配置源映射

```ts
export default defineConfig({
  build: {
    sourcemap: true, // 开发环境
    sourcemap: 'hidden', // 生产环境（隐藏但保留调试信息）
  },
})
```

### 源映射类型

```ts
export default defineConfig({
  build: {
    sourcemap: 'inline', // 内联到 JS 文件
    // 或
    sourcemap: true, // 单独的 .map 文件
  },
})
```

## 插件调试

### 插件日志

```ts
function myPlugin() {
  return {
    name: 'my-plugin',
    config(config) {
      console.log('配置:', config)
    },
    configResolved(config) {
      console.log('解析后的配置:', config)
    },
    transform(code, id) {
      console.log('转换文件:', id)
      return code
    },
  }
}
```

### 插件检查

```ts
import { defineConfig } from 'vite'
import Inspect from 'vite-plugin-inspect'

export default defineConfig({
  plugins: [
    Inspect({
      build: true,
      outputDir: '.vite-inspect',
    }),
  ],
})
```

## 模块解析调试

### 解析别名调试

```ts
export default defineConfig({
  resolve: {
    alias: {
      '@': '/src',
      'debug': true, // 启用解析调试
    },
  },
})
```

### 模块解析日志

```ts
export default defineConfig({
  server: {
    fs: {
      strict: false, // 允许访问项目外的文件
    },
  },
})
```

## HMR 调试

### HMR 日志

```ts
export default defineConfig({
  server: {
    hmr: {
      overlay: true, // 错误覆盖层
      protocol: 'ws', // 'ws' | 'wss'
    },
  },
})
```

### 自定义 HMR 处理

```ts
if (import.meta.hot) {
  console.log('HMR 已启用')

  import.meta.hot.accept((newModule) => {
    console.log('模块已更新:', newModule)
  })

  import.meta.hot.dispose((data) => {
    console.log('模块即将被替换')
  })
}
```

## 构建调试

### 构建分析

```bash
# 安装分析工具
pnpm add -D rollup-plugin-visualizer
```

```ts
import { visualizer } from 'rollup-plugin-visualizer'
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [
    visualizer({
      open: true,
      gzipSize: true,
      brotliSize: true,
      filename: 'dist/stats.html',
    }),
  ],
})
```

### 构建日志

```ts
export default defineConfig({
  build: {
    minify: false, // 禁用压缩以便调试
    sourcemap: true,
  },
})
```

## 性能调试

### 构建时间分析

```ts
import { defineConfig } from 'vite'

const startTime = Date.now()

export default defineConfig({
  plugins: [
    {
      name: 'build-timer',
      buildEnd() {
        const duration = Date.now() - startTime
        console.log(`构建耗时: ${duration}ms`)
      },
    },
  ],
})
```

### 依赖分析

```bash
# 使用 vite-plugin-circular-dependency
pnpm add -D vite-plugin-circular-dependency
```

```ts
import { defineConfig } from 'vite'
import circularDependency from 'vite-plugin-circular-dependency'

export default defineConfig({
  plugins: [
    circularDependency({
      cwd: process.cwd(),
    }),
  ],
})
```

## 浏览器调试

### Vue DevTools

```ts
import { defineConfig } from 'vite'
import VueDevTools from 'vite-plugin-vue-devtools'

export default defineConfig({
  plugins: [
    VueDevTools(),
  ],
})
```

### React DevTools

```ts
import react from '@vitejs/plugin-react'
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [
    react({
      babel: {
        plugins: ['react-refresh/babel'],
      },
    }),
  ],
})
```

### Chrome DevTools

```ts
// 在代码中添加调试断点
debugger

// 使用 console
console.log('调试信息', { data })
console.warn('警告信息')
console.error('错误信息')

// 使用 performance API
performance.mark('start')
// 执行代码
performance.mark('end')
performance.measure('操作', 'start', 'end')
```

## 网络调试

### 代理调试

```ts
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        configure: (proxy, options) => {
          proxy.on('proxyReq', (proxyReq, req, res) => {
            console.log('代理请求:', req.url)
          })
          proxy.on('proxyRes', (proxyRes, req, res) => {
            console.log('代理响应:', proxyRes.statusCode)
          })
        },
      },
    },
  },
})
```

### 网络请求监控

```ts
// 使用 Performance API
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    console.log('资源加载:', entry.name, entry.duration)
  }
})

observer.observe({ entryTypes: ['resource'] })
```

## 错误处理

### 全局错误处理

```ts
window.addEventListener('error', (event) => {
  console.error('全局错误:', event.error)
})

window.addEventListener('unhandledrejection', (event) => {
  console.error('未处理的 Promise 拒绝:', event.reason)
})
```

### Vue 错误处理

```ts
import { createApp } from 'vue'

const app = createApp(App)

app.config.errorHandler = (err, vm, info) => {
  console.error('Vue 错误:', err, info)
}
```

### React 错误边界

```tsx
class ErrorBoundary extends React.Component {
  state = { hasError: false }

  static getDerivedStateFromError(error) {
    return { hasError: true }
  }

  componentDidCatch(error, errorInfo) {
    console.error('React 错误:', error, errorInfo)
  }

  render() {
    if (this.state.hasError) {
      return <div>出错了</div>
    }
    return this.props.children
  }
}
```

## 调试工具

### Vite 插件检查器

```bash
# 安装
pnpm add -D vite-plugin-inspect

# 启动开发服务器后访问
# http://localhost:5173/__inspect/
```

### Rollup 插件可视化

```bash
# 构建后自动打开可视化报告
pnpm build

# 访问 dist/stats.html
```

## 最佳实践总结

1. **日志级别**：根据需要调整日志级别
2. **源映射**：开发和生产环境都启用源映射
3. **插件调试**：使用 vite-plugin-inspect 检查插件
4. **构建分析**：使用 rollup-plugin-visualizer 分析构建结果
5. **性能监控**：使用 Performance API 监控性能
6. **错误处理**：实现全局错误处理和错误边界
7. **DevTools**：使用浏览器 DevTools 进行调试

<!--
Source references:
- https://vite.dev/guide/troubleshooting
- https://vite.dev/guide/api-plugin
-->
