---
name: vite-troubleshooting
description: Vite 常见问题解决方案
---

# 常见问题解决方案

## 启动问题

### 端口被占用

```ts
export default defineConfig({
  server: {
    port: 3000, // 更改端口
    strictPort: false, // 自动查找可用端口
  },
})
```

### EADDRINUSE 错误

```bash
# Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F

# macOS/Linux
lsof -ti:3000 | xargs kill -9
```

### 文件监听失败

```ts
export default defineConfig({
  server: {
    watch: {
      usePolling: true, // Windows 上可能需要
      interval: 100,
    },
  },
})
```

## 构建问题

### 内存不足

```ts
export default defineConfig({
  build: {
    chunkSizeWarningLimit: 1000, // 增加警告限制
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['vue', 'react'],
        },
      },
    },
  },
})
```

### 构建超时

```bash
# 增加 Node.js 内存限制
NODE_OPTIONS="--max-old-space-size=4096" vite build
```

### 循环依赖

```bash
# 安装检测插件
pnpm add -D vite-plugin-circular-dependency
```

```ts
import { defineConfig } from 'vite'
import circularDependency from 'vite-plugin-circular-dependency'

export default defineConfig({
  plugins: [
    circularDependency({
      cwd: process.cwd(),
      include: [/src/],
      exclude: [/node_modules/],
    }),
  ],
})
```

## 依赖问题

### 依赖未找到

```ts
export default defineConfig({
  optimizeDeps: {
    include: ['some-package'], // 强制预优化
  },
})
```

### CommonJS 依赖

```ts
export default defineConfig({
  optimizeDeps: {
    exclude: ['some-commonjs-package'], // 排除预优化
  },
})
```

### 依赖版本冲突

```bash
# 清理 node_modules 和缓存
rm -rf node_modules package-lock.json pnpm-lock.yaml
pnpm install
```

## 资源问题

### 静态资源 404

```ts
export default defineConfig({
  publicDir: 'public', // 确认 public 目录
  assetsInclude: ['**/*.glb', '**/*.gltf'], // 包含其他资源
})
```

### 图片路径错误

```ts
export default defineConfig({
  build: {
    assetsDir: 'assets', // 资源输出目录
    rollupOptions: {
      output: {
        assetFileNames: 'assets/[name]-[hash][extname]',
      },
    },
  },
})
```

### 字体加载失败

```ts
export default defineConfig({
  build: {
    assetsInlineLimit: 4096, // 增加内联限制
  },
})
```

## 环境变量问题

### 环境变量未定义

```ts
// 确保使用 VITE_ 前缀
VITE_API_URL=https://api.example.com

// 在代码中访问
console.log(import.meta.env.VITE_API_URL)
```

### TypeScript 类型错误

```ts
// src/vite-env.d.ts
interface ImportMetaEnv {
  readonly VITE_API_URL: string
  readonly VITE_APP_TITLE: string
}

interface ImportMeta {
  readonly env: ImportMetaEnv
}
```

## 路径别名问题

### 别名解析失败

```ts
import { defineConfig } from 'vite'
import path from 'node:path'

export default defineConfig({
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src'),
    },
  },
})
```

### TypeScript 别名不匹配

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  }
}
```

## HMR 问题

### HMR 不工作

```ts
export default defineConfig({
  server: {
    hmr: {
      overlay: true, // 启用错误覆盖层
      protocol: 'ws', // 或 'wss'
    },
  },
})
```

### 样式不更新

```ts
export default defineConfig({
  css: {
    devSourcemap: true, // 启用 CSS 源映射
  },
})
```

## SSR 问题

### SSR 构建失败

```ts
export default defineConfig({
  ssr: {
    noExternal: ['some-package'], // 打包此依赖
    external: ['another-package'], // 外部化此依赖
  },
})
```

### 客户端水合错误

```ts
// 确保服务端和客户端渲染一致
if (import.meta.env.SSR) {
  // 仅服务端代码
}
```

## 性能问题

### 首屏加载慢

```ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['vue', 'vue-router'],
        },
      },
    },
  },
})
```

### 开发服务器慢

```ts
export default defineConfig({
  optimizeDeps: {
    force: false, // 避免强制重新预优化
  },
  server: {
    watch: {
      ignored: ['**/node_modules/**', '**/.git/**'],
    },
  },
})
```

## 兼容性问题

### 旧浏览器不支持

```ts
export default defineConfig({
  build: {
    target: ['es2015', 'chrome58', 'firefox57', 'safari11'],
  },
})
```

### Polyfill 缺失

```ts
import { defineConfig } from 'vite'
import legacy from '@vitejs/plugin-legacy'

export default defineConfig({
  plugins: [
    legacy({
      targets: ['defaults', 'not IE 11'],
      additionalLegacyPolyfills: ['regenerator-runtime/runtime'],
    }),
  ],
})
```

## Windows 特定问题

### 路径分隔符

```ts
import { defineConfig } from 'vite'
import path from 'node:path'

export default defineConfig({
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src').replace(/\\/g, '/'),
    },
  },
})
```

### 文件监听问题

```ts
export default defineConfig({
  server: {
    watch: {
      usePolling: true,
      interval: 1000,
    },
  },
})
```

## 调试技巧

### 查看配置

```ts
import { defineConfig } from 'vite'

export default defineConfig(({ mode }) => {
  console.log('当前模式:', mode)
  console.log('环境变量:', import.meta.env)
  
  return {
    // 配置
  }
})
```

### 查看依赖图

```bash
# 使用 vite-plugin-circular-dependency
pnpm add -D vite-plugin-circular-dependency
```

### 查看构建分析

```bash
# 使用 rollup-plugin-visualizer
pnpm add -D rollup-plugin-visualizer

# 构建后查看 dist/stats.html
```

## 最佳实践

1. **清理缓存**：遇到问题时先清理缓存
2. **检查依赖**：确保依赖版本兼容
3. **查看日志**：启用详细日志定位问题
4. **使用工具**：利用分析工具诊断问题
5. **逐步排查**：从简单配置开始逐步添加
6. **查看文档**：参考官方文档和社区解决方案

<!--
Source references:
- https://vite.dev/guide/troubleshooting
- https://vite.dev/faq
-->
