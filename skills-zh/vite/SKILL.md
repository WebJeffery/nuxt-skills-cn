---
name: vite
description: Vite 构建工具配置、插件 API、SSR 和 Vite 8 Rolldown 迁移。在处理 Vite 项目、vite.config.ts、Vite 插件或使用 Vite 构建库/SSR 应用时使用。
metadata:
  author: Anthony Fu
  version: "2026.1.31"
  source: Generated from https://github.com/vitejs/vite, scripts at https://github.com/antfu/skills
---

# Vite

> 基于 Vite 8 beta（使用 Rolldown）。Vite 8 使用 Rolldown 打包器和 Oxc 转换器。

Vite 是下一代前端构建工具，具有快速的开发服务器（原生 ESM + HMR）和优化的生产构建。

## 偏好

- 使用 TypeScript：优先使用 `vite.config.ts`
- 始终使用 ESM，避免 CommonJS

## 核心

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 配置 | `vite.config.ts`、`defineConfig`、条件配置、`loadEnv` | [core-config](references/core-config.md) |
| 功能 | `import.meta.glob`、资源查询（`?raw`、`?url`）、`import.meta.env`、HMR API | [core-features](references/core-features.md) |
| 插件 API | Vite 特定钩子、虚拟模块、插件顺序 | [core-plugin-api](references/core-plugin-api.md) |

## 构建与 SSR

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 构建与 SSR | 库模式、SSR 中间件模式、`ssrLoadModule`、JavaScript API | [build-and-ssr](references/build-and-ssr.md) |

## 高级

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 环境 API | Vite 6+ 多环境支持、自定义运行时 | [environment-api](references/environment-api.md) |
| Rolldown 迁移 | Vite 8 变更：Rolldown 打包器、Oxc 转换器、配置迁移 | [rolldown-migration](references/rolldown-migration.md) |

## 最佳实践

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 性能优化 | 开发服务器优化、生产构建优化、资源优化、构建分析 | [best-practices-performance](references/best-practices-performance.md) |
| 开发体验 | 开发服务器配置、HMR 优化、类型提示、路径别名 | [best-practices-dev-experience](references/best-practices-dev-experience.md) |
| 代码分割 | 动态导入、路由懒加载、按功能分割、CSS 分割 | [best-practices-code-splitting](references/best-practices-code-splitting.md) |
| 缓存策略 | 依赖预优化缓存、构建缓存、浏览器缓存、CDN 缓存 | [best-practices-caching](references/best-practices-caching.md) |
| 调试技巧 | 源映射调试、插件调试、构建分析、浏览器调试 | [best-practices-debugging](references/best-practices-debugging.md) |
| 故障排除 | 启动问题、构建问题、依赖问题、兼容性问题 | [best-practices-troubleshooting](references/best-practices-troubleshooting.md) |
| 安全最佳实践 | 环境变量安全、CSP、HTTPS、依赖安全、XSS 防护 | [best-practices-security](references/best-practices-security.md) |

## 快速参考

### CLI 命令

```bash
vite              # 启动开发服务器
vite build        # 生产构建
vite preview      # 预览生产构建
vite build --ssr  # SSR 构建
```

### 常用配置

```ts
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [],
  resolve: { alias: { '@': '/src' } },
  server: { port: 3000, proxy: { '/api': 'http://localhost:8080' } },
  build: { target: 'esnext', outDir: 'dist' },
})
```

### 官方插件

- `@vitejs/plugin-vue` - Vue 3 SFC 支持
- `@vitejs/plugin-vue-jsx` - Vue 3 JSX
- `@vitejs/plugin-react` - React（使用 Oxc/Babel）
- `@vitejs/plugin-react-swc` - React（使用 SWC）
- `@vitejs/plugin-legacy` - 旧版浏览器支持
