---
name: vite-dev-experience
description: Vite 开发体验优化最佳实践
---

# 开发体验优化

## 开发服务器配置

### 自动打开浏览器

```ts
export default defineConfig({
  server: {
    open: true, // 自动打开浏览器
    open: '/docs', // 打开特定路径
  },
})
```

### 端口和主机配置

```ts
export default defineConfig({
  server: {
    port: 3000, // 指定端口
    strictPort: true, // 端口被占用时报错
    host: true, // 监听所有地址
  },
})
```

### HTTPS 配置

```ts
export default defineConfig({
  server: {
    https: {
      key: fs.readFileSync('key.pem'),
      cert: fs.readFileSync('cert.pem'),
    },
  },
})
```

### 代理配置

```ts
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, ''),
      },
      '/ws': {
        target: 'ws://localhost:8080',
        ws: true,
      },
    },
  },
})
```

## HMR 优化

### HMR 连接配置

```ts
export default defineConfig({
  server: {
    hmr: {
      overlay: true, // 错误覆盖层
      port: 24678, // HMR 端口
    },
  },
})
```

### 自定义 HMR 处理

```ts
if (import.meta.hot) {
  import.meta.hot.accept((newModule) => {
    console.log('模块已更新')
  })
}
```

## 源映射配置

### 开发环境源映射

```ts
export default defineConfig({
  build: {
    sourcemap: true, // 生成源映射
  },
})
```

### 生产环境源映射

```ts
export default defineConfig({
  build: {
    sourcemap: 'hidden', // 隐藏源映射但保留调试信息
  },
})
```

## 类型提示

### 自动导入类型

```ts
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/rspack'

export default defineConfig({
  plugins: [
    AutoImport({
      imports: ['vue', 'vue-router'],
      dts: 'src/auto-imports.d.ts', // 生成类型声明
    }),
  ],
})
```

### 组件类型提示

```ts
import { defineConfig } from 'vite'
import Components from 'unplugin-vue-components/rspack'

export default defineConfig({
  plugins: [
    Components({
      dts: 'src/components.d.ts', // 生成组件类型
    }),
  ],
})
```

## 路径别名

### 配置路径别名

```ts
import { defineConfig } from 'vite'
import path from 'node:path'

export default defineConfig({
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src'),
      '@components': path.resolve(__dirname, 'src/components'),
      '@utils': path.resolve(__dirname, 'src/utils'),
    },
  },
})
```

### TypeScript 配置

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@components/*": ["src/components/*"],
      "@utils/*": ["src/utils/*"]
    }
  }
}
```

## 环境变量管理

### 多环境配置

```bash
# .env.development
VITE_API_URL=http://localhost:3000

# .env.production
VITE_API_URL=https://api.example.com

# .env.staging
VITE_API_URL=https://staging-api.example.com
```

### 环境变量类型定义

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

## 插件推荐

### 代码格式化

```ts
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [
    // ESLint
    eslintPlugin({
      cache: false,
      include: ['src/**/*.ts', 'src/**/*.tsx', 'src/**/*.vue'],
    }),
    // Prettier
    stylelint(),
  ],
})
```

### Git Hooks

```ts
import { defineConfig } from 'vite'
import { vitePluginFengari } from 'vite-plugin-fengari'

export default defineConfig({
  plugins: [
    vitePluginFengari({
      'pre-commit': 'lint-staged',
    }),
  ],
})
```

### 路径自动补全

```ts
import { defineConfig } from 'vite'
import { PathAutocomplete } from 'vite-plugin-path-autocomplete'

export default defineConfig({
  plugins: [
    PathAutocomplete(),
  ],
})
```

## 调试工具

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
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

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

## 构建输出优化

### 清理输出目录

```ts
import { defineConfig } from 'vite'
import { rmSync } from 'node:fs'

export default defineConfig({
  plugins: [
    {
      name: 'clean-dist',
      closeBundle() {
        rmSync('dist', { recursive: true, force: true })
      },
    },
  ],
})
```

### 文件复制

```ts
import { defineConfig } from 'vite'
import viteStaticCopy from 'vite-plugin-static-copy'

export default defineConfig({
  plugins: [
    viteStaticCopy({
      targets: [
        { src: 'public', dest: 'dist' },
        { src: 'src/assets/images', dest: 'dist/images' },
      ],
    }),
  ],
})
```

## 最佳实践总结

1. **开发服务器**：配置自动打开、端口、代理等提升开发效率
2. **HMR**：启用错误覆盖层，优化热更新体验
3. **类型提示**：使用自动导入和组件类型提示
4. **路径别名**：简化导入路径，提高可读性
5. **环境变量**：合理管理多环境配置
6. **插件生态**：利用社区插件增强开发体验
7. **调试工具**：集成 DevTools 提升调试效率

<!--
Source references:
- https://vite.dev/guide/
- https://vite.dev/config/server-options
-->
