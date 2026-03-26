---
name: vite-environment-api
description: Vite 6+ 多运行时环境的环境 API
---

# 环境 API (Vite 6+)

环境 API 将多个运行时环境形式化,超越传统的客户端/SSR 分离。

## 概念

Vite 6 之前: 两个隐式环境(`client` 和 `ssr`)。

Vite 6+: 根据需要配置任意数量的环境(浏览器、node 服务器、edge 服务器等)。

## 基本配置

对于 SPA/MPA,没有任何变化——选项应用于隐式 `client` 环境:

```ts
export default defineConfig({
  build: { sourcemap: false },
  optimizeDeps: { include: ['lib'] },
})
```

## 多个环境

```ts
export default defineConfig({
  build: { sourcemap: false },  // 被所有环境继承
  optimizeDeps: { include: ['lib'] },  // 仅客户端
  environments: {
    // SSR 环境
    server: {},
    // Edge 运行时环境
    edge: {
      resolve: { noExternal: true },
    },
  },
})
```

环境继承顶级配置。某些选项(如 `optimizeDeps`)默认仅应用于 `client`。

## 环境选项

```ts
interface EnvironmentOptions {
  define?: Record<string, any>
  resolve?: EnvironmentResolveOptions
  optimizeDeps: DepOptimizationOptions
  consumer?: 'client' | 'server'
  dev: DevOptions
  build: BuildOptions
}
```

## 自定义环境实例

运行时提供程序可以定义自定义环境:

```ts
import { customEnvironment } from 'vite-environment-provider'

export default defineConfig({
  environments: {
    ssr: customEnvironment({
      build: { outDir: '/dist/ssr' },
    }),
  },
})
```

示例: Cloudflare 的 Vite 插件在开发期间在 `workerd` 运行时中运行代码。

## 向后兼容性

- `server.moduleGraph` 返回混合的客户端/SSR 视图
- `ssrLoadModule` 仍然有效
- 现有的 SSR 应用程序保持不变

## 何时使用

- **最终用户**: 通常不需要配置——框架处理它
- **插件作者**: 使用它进行环境感知转换
- **框架作者**: 为其运行时需求创建自定义环境

## 插件环境访问

插件可以在钩子中访问环境:

```ts
{
  name: 'env-aware',
  transform(code, id, options) {
    if (options?.ssr) {
      // SSR 特定转换
    }
  },
}
```

<!--
Source references:
- https://vite.dev/guide/api-environment
- https://vite.dev/blog/announcing-vite6
-->
