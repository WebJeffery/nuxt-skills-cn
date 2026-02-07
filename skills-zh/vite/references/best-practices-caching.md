---
name: vite-caching
description: Vite 缓存策略最佳实践
---

# 缓存策略

## 依赖预优化缓存

### 预优化配置

```ts
export default defineConfig({
  optimizeDeps: {
    // 缓存目录
    cacheDir: 'node_modules/.vite',
    // 强制重新预优化
    force: false,
    // 包含的依赖
    include: ['vue', 'react', 'lodash'],
    // 排除的依赖
    exclude: ['some-dev-only-package'],
    // 禁用预优化
    disabled: false,
  },
})
```

### 清理预优化缓存

```bash
# 手动清理
rm -rf node_modules/.vite

# 或使用 Vite 命令
vite build --force
vite dev --force
```

## 构建缓存

### 构建缓存配置

```ts
export default defineConfig({
  build: {
    // 缓存目录
    cacheDir: 'node_modules/.vite',
    // 清理缓存
    emptyOutDir: true,
  },
})
```

### 禁用构建缓存

```ts
export default defineConfig({
  build: {
    cacheDir: false, // 禁用缓存
  },
})
```

## 源映射缓存

### 源映射配置

```ts
export default defineConfig({
  build: {
    sourcemap: true,
    // 生产环境使用隐藏源映射
    sourcemap: 'hidden',
  },
})
```

## 浏览器缓存

### 资源文件名哈希

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        // 文件名包含哈希
        entryFileNames: 'assets/[name]-[hash].js',
        chunkFileNames: 'assets/[name]-[hash].js',
        assetFileNames: 'assets/[name]-[hash][extname]',
      },
    },
  },
})
```

### 静态资源缓存

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        // 不带哈希的文件（长期缓存）
        assetFileNames: (assetInfo) => {
          if (assetInfo.name === 'favicon.ico') {
            return 'favicon.ico'
          }
          return 'assets/[name]-[hash][extname]'
        },
      },
    },
  },
})
```

## HTTP 缓存头

### 开发服务器缓存头

```ts
export default defineConfig({
  server: {
    headers: {
      'Cache-Control': 'no-cache',
    },
  },
})
```

### 预览服务器缓存头

```ts
export default defineConfig({
  preview: {
    headers: {
      'Cache-Control': 'public, max-age=31536000',
    },
  },
})
```

## CDN 缓存

### CDN 配置

```ts
export default defineConfig({
  base: 'https://cdn.example.com/assets/',
  build: {
    rollupOptions: {
      output: {
        assetFileNames: 'assets/[name]-[hash][extname]',
      },
    },
  },
})
```

### CDN 预加载

```html
<!DOCTYPE html>
<html>
<head>
  <link rel="preconnect" href="https://cdn.example.com">
  <link rel="dns-prefetch" href="https://cdn.example.com">
</head>
<body>
  <div id="app"></div>
  <script type="module" src="/assets/index-[hash].js"></script>
</body>
</html>
```

## Service Worker 缓存

### Workbox 集成

```ts
import { defineConfig } from 'vite'
import { VitePWA } from 'vite-plugin-pwa'

export default defineConfig({
  plugins: [
    VitePWA({
      strategies: 'networkFirst',
      cacheId: 'my-app-v1',
      registerType: 'autoUpdate',
      workbox: {
        globPatterns: ['**/*.{js,css,html,ico,png,svg}'],
        runtimeCaching: [
          {
            urlPattern: /^https:\/\/api\.example\.com\/.*/,
            handler: 'NetworkFirst',
            options: {
              cacheName: 'api-cache',
              expiration: {
                maxEntries: 100,
                maxAgeSeconds: 60 * 60 * 24,
              },
            },
          },
        ],
      },
    }),
  ],
})
```

### 自定义 Service Worker

```ts
// sw.js
const CACHE_NAME = 'my-app-v1'
const urlsToCache = ['/']

self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME).then((cache) => {
      return cache.addAll(urlsToCache)
    })
  )
})

self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request).then((response) => {
      return response || fetch(event.request)
    })
  )
})
```

## 内存缓存

### React 缓存

```tsx
import { useMemo, useCallback } from 'react'

function ExpensiveComponent({ data }) {
  const processedData = useMemo(() => {
    return expensiveOperation(data)
  }, [data])

  const handleClick = useCallback(() => {
    console.log('clicked')
  }, [])

  return <div onClick={handleClick}>{processedData}</div>
}
```

### Vue 缓存

```vue
<template>
  <div>{{ computedValue }}</div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps(['data'])

const computedValue = computed(() => {
  return expensiveOperation(props.data)
})
</script>
```

## 缓存失效策略

### 版本控制

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        // 使用内容哈希
        entryFileNames: 'assets/[name]-[hash].js',
        chunkFileNames: 'assets/[name]-[hash].js',
        assetFileNames: 'assets/[name]-[hash][extname]',
      },
    },
  },
})
```

### 缓存清理

```ts
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [
    {
      name: 'clean-cache',
      buildStart() {
        console.log('清理旧缓存...')
      },
    },
  ],
})
```

## 最佳实践总结

1. **依赖缓存**：利用 Vite 的预优化缓存，必要时手动清理
2. **构建缓存**：启用构建缓存加速重复构建
3. **浏览器缓存**：使用哈希文件名和适当的缓存头
4. **CDN 缓存**：将静态资源部署到 CDN
5. **Service Worker**：使用 PWA 技术实现离线缓存
6. **内存缓存**：在应用层面使用 useMemo、computed 等
7. **缓存失效**：使用版本控制和哈希确保缓存更新

<!--
Source references:
- https://vite.dev/guide/build
- https://vite.dev/config/dep-optimization-options
-->
