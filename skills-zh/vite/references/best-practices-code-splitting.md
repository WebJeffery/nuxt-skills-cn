---
name: vite-code-splitting
description: Vite 代码分割策略最佳实践
---

# 代码分割策略

## 基础代码分割

### 自动代码分割

Vite 默认使用 Rollup 进行代码分割：

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        // 自动分割
        manualChunks: undefined,
      },
    },
  },
})
```

### 手动代码分割

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          // 将 node_modules 中的依赖打包到 vendor
          vendor: ['vue', 'vue-router', 'pinia'],
          // UI 库单独打包
          'ui-lib': ['element-plus', '@element-plus/icons-vue'],
          // 工具库单独打包
          utils: ['lodash-es', 'dayjs'],
        },
      },
    },
  },
})
```

## 动态导入

### 路由懒加载

```ts
// Vue Router
const routes = [
  {
    path: '/about',
    component: () => import('./views/About.vue'),
  },
]
```

```tsx
// React Router
const Home = lazy(() => import('./pages/Home'))
const About = lazy(() => import('./pages/About'))

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </Suspense>
  )
}
```

### 条件导入

```ts
async function loadFeature() {
  if (condition) {
    const module = await import('./feature-a')
    return module.default
  } else {
    const module = await import('./feature-b')
    return module.default
  }
}
```

### 预加载

```ts
// 预加载模块
import('./heavy-module.js')

// 预加载并缓存
const modulePromise = import('./heavy-module.js')

// 使用时直接获取
const module = await modulePromise
```

## 按路由分割

### Vue 路由分割

```ts
// router/index.ts
const routes = [
  {
    path: '/',
    component: () => import('@/views/Home/index.vue'),
  },
  {
    path: '/dashboard',
    component: () => import('@/views/Dashboard/index.vue'),
    children: [
      {
        path: 'overview',
        component: () => import('@/views/Dashboard/Overview.vue'),
      },
    ],
  },
]
```

### React 路由分割

```tsx
// routes.tsx
import { lazy } from 'react'

const routes = [
  {
    path: '/',
    element: lazy(() => import('./pages/Home')),
  },
  {
    path: '/dashboard',
    element: lazy(() => import('./pages/Dashboard')),
    children: [
      {
        path: 'overview',
        element: lazy(() => import('./pages/Dashboard/Overview')),
      },
    ],
  },
]
```

## 按功能分割

### 功能模块分割

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks(id) {
          // 将不同功能的模块分开打包
          if (id.includes('src/features/auth')) {
            return 'auth'
          }
          if (id.includes('src/features/user')) {
            return 'user'
          }
          if (id.includes('src/features/dashboard')) {
            return 'dashboard'
          }
          if (id.includes('node_modules')) {
            return 'vendor'
          }
        },
      },
    },
  },
})
```

### 第三方库分割

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks(id) {
          // 按库的类型分割
          if (id.includes('node_modules')) {
            if (id.includes('vue')) {
              return 'vendor-vue'
            }
            if (id.includes('react')) {
              return 'vendor-react'
            }
            if (id.includes('lodash')) {
              return 'vendor-lodash'
            }
            return 'vendor'
          }
        },
      },
    },
  },
})
```

## CSS 代码分割

### 启用 CSS 代码分割

```ts
export default defineConfig({
  build: {
    cssCodeSplit: true, // 默认启用
  },
})
```

### CSS 按需加载

```ts
// 动态导入 CSS
import('./styles/theme-dark.css')

// 条件加载 CSS
if (isDark) {
  await import('./styles/dark.css')
} else {
  await import('./styles/light.css')
}
```

## 优化策略

### 共享依赖提取

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          // 提取共享依赖
          'shared': ['vue', 'vue-router'],
          // 各页面独立打包
        },
      },
    },
  },
})
```

### 避免过度分割

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        // 设置最小块大小
        minChunkSize: 20000, // 20kb
        // 设置最大块大小
        maxParallelFileOps: 5,
      },
    },
  },
})
```

### 按需导入

```ts
// Lodash 按需导入
import { debounce } from 'lodash-es'

// Element Plus 按需导入
import { ElButton } from 'element-plus'

// Ant Design 按需导入
import { Button } from 'antd'
```

## 性能优化

### 预加载关键资源

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        // 使用 preload/prefetch
        manualChunks(id) {
          if (id.includes('src/pages/Home')) {
            return 'home'
          }
        },
      },
    },
  },
})
```

### 延迟加载非关键资源

```ts
// 使用 Intersection Observer 延迟加载
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      import('./heavy-component')
      observer.unobserve(entry.target)
    }
  })
})

observer.observe(document.getElementById('lazy-section'))
```

## 监控和分析

### 分析代码分割

```bash
# 安装分析工具
pnpm add -D rollup-plugin-visualizer
```

```ts
import { defineConfig } from 'vite'
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  plugins: [
    visualizer({
      filename: 'dist/stats.html',
      open: true,
      gzipSize: true,
      brotliSize: true,
    }),
  ],
})
```

## 最佳实践总结

1. **路由级别**：对路由组件使用动态导入
2. **功能级别**：按功能模块分割代码
3. **依赖级别**：将第三方库单独打包
4. **共享依赖**：提取公共依赖到独立 chunk
5. **避免过度**：不要过度分割，考虑网络开销
6. **按需导入**：使用按需导入减少包体积
7. **持续监控**：使用分析工具定期检查分割效果

<!--
Source references:
- https://vite.dev/guide/build
- https://rollupjs.org/plugin-development/#outputchunks
-->
