---
name: vite-features
description: Vite 特定的导入模式和运行时功能
---

# Vite 功能

## Glob 导入

导入匹配模式的多个模块:

```ts
const modules = import.meta.glob('./dir/*.ts')
// { './dir/foo.ts': () => import('./dir/foo.ts'), ... }

for (const path in modules) {
  modules[path]().then((mod) => {
    console.log(path, mod)
  })
}
```

### 急切加载

```ts
const modules = import.meta.glob('./dir/*.ts', { eager: true })
// 模块立即加载,无动态导入
```

### 命名导入

```ts
const modules = import.meta.glob('./dir/*.ts', { import: 'setup' })
// 仅从每个模块导入 'setup' 导出

const defaults = import.meta.glob('./dir/*.ts', { import: 'default', eager: true })
```

### 多个模式

```ts
const modules = import.meta.glob(['./dir/*.ts', './another/*.ts'])
```

### 否定模式

```ts
const modules = import.meta.glob(['./dir/*.ts', '!**/ignored.ts'])
```

### 自定义查询

```ts
const svgRaw = import.meta.glob('./icons/*.svg', { query: '?raw', import: 'default' })
const svgUrls = import.meta.glob('./icons/*.svg', { query: '?url', import: 'default' })
```

## 资源导入查询

### URL 导入

```ts
import imgUrl from './img.png'
// 返回解析的 URL: '/src/img.png' (开发) 或 '/assets/img.2d8efhg.png' (构建)
```

### 显式 URL

```ts
import workletUrl from './worklet.js?url'
```

### 原始字符串

```ts
import shaderCode from './shader.glsl?raw'
```

### 内联/非内联

```ts
import inlined from './small.png?inline'    // 强制 base64 内联
import notInlined from './large.png?no-inline'  // 强制单独文件
```

### Web Workers

```ts
import Worker from './worker.ts?worker'
const worker = new Worker()

// 或内联:
import InlineWorker from './worker.ts?worker&inline'
```

首选使用构造函数的模式:

```ts
const worker = new Worker(new URL('./worker.ts', import.meta.url), {
  type: 'module',
})
```

## 环境变量

### 内置常量

```ts
import.meta.env.MODE      // 'development' | 'production' | 自定义
import.meta.env.BASE_URL  // 来自配置的基础 URL
import.meta.env.PROD      // 生产中为 true
import.meta.env.DEV       // 开发中为 true
import.meta.env.SSR       // 在服务器中运行时为 true
```

### 自定义变量

只有 `VITE_` 前缀的变量暴露给客户端:

```
# .env
VITE_API_URL=https://api.example.com
DB_PASSWORD=secret  # 不暴露给客户端
```

```ts
console.log(import.meta.env.VITE_API_URL) // 有效
console.log(import.meta.env.DB_PASSWORD)  // undefined
```

### 模式特定文件

```
.env                # 始终加载
.env.local          # 始终加载,gitignored
.env.[mode]         # 仅在指定模式中
.env.[mode].local   # 仅在指定模式中,gitignored
```

### TypeScript 支持

```ts
// vite-env.d.ts
interface ImportMetaEnv {
  readonly VITE_API_URL: string
}

interface ImportMeta {
  readonly env: ImportMetaEnv
}
```

### HTML 替换

```html
<p>在 %MODE% 中运行</p>
<script>window.API = "%VITE_API_URL%"</script>
```

## CSS 模块

任何 `.module.css` 文件都被视为 CSS 模块:

```ts
import styles from './component.module.css'
element.className = styles.button
```

使用 camelCase 转换:

```ts
// .my-class -> myClass (如果配置了 css.modules.localsConvention)
import { myClass } from './component.module.css'
```

## JSON 导入

```ts
import pkg from './package.json'
import { version } from './package.json'  // 带有 tree-shaking 的命名导入
```

## HMR API

```ts
if (import.meta.hot) {
  import.meta.hot.accept((newModule) => {
    // 处理更新
  })

  import.meta.hot.dispose((data) => {
    // 在模块替换之前清理
  })

  import.meta.hot.invalidate()  // 强制完全重新加载
}
```

<!--
Source references:
- https://vite.dev/guide/features
- https://vite.dev/guide/env-and-mode
- https://vite.dev/guide/assets
- https://vite.dev/guide/api-hmr
-->
