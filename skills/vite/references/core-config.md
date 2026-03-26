---
name: vite-config
description: 使用 vite.config.ts 的 Vite 配置模式
---

# Vite 配置

## 基本设置

```ts
// vite.config.ts
import { defineConfig } from 'vite'

export default defineConfig({
  // 配置选项
})
```

Vite 从项目根目录自动解析 `vite.config.ts`。无论 `package.json` 类型如何,都支持 ES 模块语法。

## 条件配置

导出函数以访问命令和模式:

```ts
export default defineConfig(({ command, mode, isSsrBuild, isPreview }) => {
  if (command === 'serve') {
    return { /* 开发配置 */ }
  } else {
    return { /* 构建配置 */ }
  }
})
```

- `command`: 开发期间为 `'serve'`,生产构建为 `'build'`
- `mode`: `'development'` 或 `'production'`(或通过 `--mode` 自定义)

## 异步配置

```ts
export default defineConfig(async ({ command, mode }) => {
  const data = await fetchSomething()
  return { /* 配置 */ }
})
```

## 在配置中使用环境变量

`.env` 文件在配置解析**之后**加载。使用 `loadEnv` 在配置中访问它们:

```ts
import { defineConfig, loadEnv } from 'vite'

export default defineConfig(({ mode }) => {
  // 从 cwd 加载环境文件,包含所有变量(空前缀)
  const env = loadEnv(mode, process.cwd(), '')

  return {
    define: {
      __APP_ENV__: JSON.stringify(env.APP_ENV),
    },
    server: {
      port: env.APP_PORT ? Number(env.APP_PORT) : 5173,
    },
  }
})
```

## 关键配置选项

### resolve.alias

```ts
export default defineConfig({
  resolve: {
    alias: {
      '@': '/src',
      '~': '/src',
    },
  },
})
```

### define (全局常量)

```ts
export default defineConfig({
  define: {
    __APP_VERSION__: JSON.stringify('1.0.0'),
    __API_URL__: 'window.__backend_api_url',
  },
})
```

值必须是 JSON 可序列化的或单个标识符。非字符串自动用 `JSON.stringify` 包装。

### plugins

```ts
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
})
```

插件数组被展平;假值被忽略。

### server.proxy

```ts
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, ''),
      },
    },
  },
})
```

### build.target

默认: 基准广泛可用的浏览器。自定义:

```ts
export default defineConfig({
  build: {
    target: 'esnext', // 或 'es2020', ['chrome90', 'firefox88']
  },
})
```

## TypeScript 智能感知

对于纯 JS 配置文件:

```js
/** @type {import('vite').UserConfig} */
export default {
  // ...
}
```

或使用 `satisfies`:

```ts
import type { UserConfig } from 'vite'

export default {
  // ...
} satisfies UserConfig
```

<!--
Source references:
- https://vite.dev/config/
- https://vite.dev/guide/
-->
