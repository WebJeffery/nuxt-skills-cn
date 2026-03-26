# Svelte 支持

使用 `tsdown` 和 `rollup-plugin-svelte` 构建 Svelte 组件库。

## 快速开始

```bash
npx create-tsdown@latest -t svelte
```

## 配置

```ts
import svelte from 'rollup-plugin-svelte'
import { sveltePreprocess } from 'svelte-preprocess'
import { defineConfig } from 'tsdown'

export default defineConfig({
  entry: ['./src/index.ts'],
  platform: 'neutral',
  plugins: [svelte({ preprocess: sveltePreprocess() })],
})
```

## 依赖

```bash
npm install -D rollup-plugin-svelte svelte svelte-preprocess
```

## 分发策略

**推荐：分发 `.svelte` 源文件**而不是预编译的 JS。让消费者的工具链（Vite + `@sveltejs/vite-plugin-svelte`）在他们的应用中编译。

原因：
- 避免与 `svelte/internal` 的版本兼容性问题
- 更好的 SSR/hydration 一致性
- 消费者获得更好的 HMR、诊断和 tree-shaking
- Svelte 升级时更少的重新发布周期

**例外**情况下分发 JS 是合理的：
- 通过 `customElement` 模式的 Web Components
- CDN 直接加载而不需要构建步骤

## 关键点

- 将 `svelte`/`svelte/*` 标记为外部；在 `peerDependencies` 中声明 `svelte`
- 使用 `svelte2tsx` 为 Svelte 组件发出 `.d.ts`
- 保持 `.svelte` 源形式用于分发

## 相关内容

- [插件](advanced-plugins.md) - 插件配置
- [依赖](option-dependencies.md) - 外部依赖