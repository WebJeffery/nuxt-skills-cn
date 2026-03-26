---
name: configuration
description: Nuxt 配置文件，包括 nuxt.config.ts、app.config.ts 和运行时配置
---

# Nuxt 配置

Nuxt 使用配置文件来自定义应用程序行为。主要的配置选项是 `nuxt.config.ts` 用于构建设置和 `app.config.ts` 用于运行时设置。

## nuxt.config.ts

项目根目录下的主配置文件：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  // 配置选项
  devtools: { enabled: true },
  modules: ['@nuxt/ui'],
})
```

### 环境覆盖

配置特定环境的设置：

```ts
export default defineNuxtConfig({
  $production: {
    routeRules: {
      '/**': { isr: true },
    },
  },
  $development: {
    // 开发环境特定配置
  },
  $env: {
    staging: {
      // 预发布环境配置
    },
  },
})
```

使用 `--envName` 标志选择环境：`nuxt build --envName staging`

## 运行时配置

对于需要通过环境变量覆盖的值：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  runtimeConfig: {
    // 仅服务器端的密钥
    apiSecret: '123',
    // public 中的键暴露给客户端
    public: {
      apiBase: '/api',
    },
  },
})
```

通过环境变量覆盖：

```ini
# .env
NUXT_API_SECRET=api_secret_token
NUXT_PUBLIC_API_BASE=https://api.example.com
```

在 components/composables 中访问：

```vue
<script setup lang="ts">
const config = useRuntimeConfig()
// 服务器端：config.apiSecret, config.public.apiBase
// 客户端：仅 config.public.apiBase
</script>
```

## App 配置

对于在构建设置的公开令牌（无法通过环境变量覆盖）：

```ts
// app/app.config.ts
export default defineAppConfig({
  title: 'Hello Nuxt',
  theme: {
    dark: true,
    colors: {
      primary: '#ff0000',
    },
  },
})
```

在组件中访问：

```vue
<script setup lang="ts">
const appConfig = useAppConfig()
</script>
```

## runtimeConfig vs app.config

| 特性 | runtimeConfig | app.config |
|---------|--------------|------------|
| 客户端 | 水合 | 打包 |
| 环境变量 | 是 | 否 |
| 响应式 | 是 | 是 |
| 热模块替换 | 否 | 是 |
| 非原始 JS 类型 | 否 | 是 |

**使用 runtimeConfig** 用于密钥和随环境变化的值。
**使用 app.config** 用于公开令牌、主题设置和非敏感配置。

## 外部工具配置

Nuxt 使用 `nuxt.config.ts` 作为单一真实源。在其中配置外部工具：

```ts
export default defineNuxtConfig({
  // Nitro 配置
  nitro: {
    // nitro 选项
  },
  // Vite 配置
  vite: {
    // vite 选项
    vue: {
      // @vitejs/plugin-vue 选项
    },
  },
  // PostCSS 配置
  postcss: {
    // postcss 选项
  },
})
```

## Vue 配置

启用 Vue 实验性功能：

```ts
export default defineNuxtConfig({
  vue: {
    propsDestructure: true,
  },
})
```

## 配置最佳实践

### 类型安全的配置

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  runtimeConfig: {
    apiSecret: process.env.API_SECRET,
    public: {
      apiBase: process.env.NUXT_PUBLIC_API_BASE,
    },
  },
})
```

### 模块配置

```ts
export default defineNuxtConfig({
  modules: [
    '@nuxtjs/color-mode',
    '@nuxt/ui',
    '@pinia/nuxt',
    '@vueuse/nuxt',
  ],
  
  colorMode: {
    classSuffix: '',
    preference: 'dark',
  },
})
```

### 性能优化配置

```ts
export default defineNuxtConfig({
  build: {
    analyze: process.env.ANALYZE === 'true',
  },
  
  experimental: {
    typedPages: true,
    payloadExtraction: true,
  },
  
  nitro: {
    compressPublicAssets: true,
  },
})
```

### SSR 配置

```ts
export default defineNuxtConfig({
  ssr: true,
  
  routeRules: {
    '/': { prerender: true },
    '/admin/**': { ssr: false },
    '/api/**': { cors: true },
  },
})
```

### CSS 配置

```ts
export default defineNuxtConfig({
  css: ['~/assets/css/main.css'],
  
  vite: {
    css: {
      modules: {
        localsConvention: 'camelCase',
      },
    },
  },
})
```

### 自动导入配置

```ts
export default defineNuxtConfig({
  imports: {
    dirs: [
      './composables',
      './utils',
    ],
  },
})
```

### 开发工具配置

```ts
export default defineNuxtConfig({
  devtools: {
    enabled: true,
    timeline: {
      enabled: true,
    },
  },
})
```

### 安全性配置

```ts
export default defineNuxtConfig({
  routeRules: {
    '/api/**': {
      headers: {
        'X-Frame-Options': 'DENY',
        'X-Content-Type-Options': 'nosniff',
      },
    },
  },
})
```

## 配置继承

### 使用 Layers

```ts
export default defineNuxtConfig({
  extends: [
    './base-layer',
    '@nuxt/ui',
  ],
})
```

### 环境特定配置

```ts
export default defineNuxtConfig({
  $development: {
    nitro: {
      experimental: {
        openAPI: true,
      },
    },
  },
  
  $production: {
    routeRules: {
      '/**': { isr: 60 },
    },
  },
})
```

## 配置调试

```ts
export default defineNuxtConfig({
  debug: true,
  typescript: {
    strict: true,
  },
})
```

## 常见问题

### 环境变量未生效

```ts
// 确保在 nuxt.config.ts 中正确使用
export default defineNuxtConfig({
  runtimeConfig: {
    private: process.env.PRIVATE_VAR, // 错误
    privateKey: process.env.PRIVATE_VAR, // 正确
  },
})
```

### 配置合并问题

```ts
// 使用深度合并
export default defineNuxtConfig({
  app: {
    head: {
      meta: [
        { charset: 'utf-8' },
      ],
    },
  },
})
```

<!-- 
源码参考：
- https://nuxt.com/docs/getting-started/configuration
- https://nuxt.com/docs/guide/going-further/runtime-config
- https://nuxt.com/docs/api/nuxt-config
-->