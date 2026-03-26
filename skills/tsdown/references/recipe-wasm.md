# WASM 支持

在 TypeScript/JavaScript 项目中打包 WebAssembly 模块。

## 概述

tsdown 通过 [`rolldown-plugin-wasm`](https://github.com/sxzz/rolldown-plugin-wasm) 支持 WASM，支持同步和异步实例化的直接 `.wasm` 导入。

## 设置

### 安装

```bash
pnpm add -D rolldown-plugin-wasm
```

### 配置

```ts
import { wasm } from 'rolldown-plugin-wasm'
import { defineConfig } from 'tsdown'

export default defineConfig({
  entry: ['./src/index.ts'],
  plugins: [wasm()],
})
```

### TypeScript 支持

将类型声明添加到 `tsconfig.json`：

```jsonc
{
  "compilerOptions": {
    "types": ["rolldown-plugin-wasm/types"]
  }
}
```

## 导入 WASM 模块

### 直接导入

```ts
import { add } from './add.wasm'
add(1, 2)
```

### 异步初始化

使用 `?init` 查询进行异步初始化：

```ts
import init from './add.wasm?init'
const instance = await init(imports) // imports 可选
instance.exports.add(1, 2)
```

### 同步初始化

使用 `?init&sync` 查询进行同步初始化：

```ts
import initSync from './add.wasm?init&sync'
const instance = initSync(imports) // imports 可选
instance.exports.add(1, 2)
```

## wasm-bindgen 支持

### 目标 `bundler`（推荐）

```ts
import { add } from 'some-pkg'
add(1, 2)
```

### 目标 `web`（Node.js）

```ts
import { readFile } from 'node:fs/promises'
import init, { add } from 'some-pkg'
import wasmUrl from 'some-pkg/add_bg.wasm?url'

await init({
  module_or_path: readFile(new URL(wasmUrl, import.meta.url)),
})
add(1, 2)
```

### 目标 `web`（浏览器）

```ts
import init, { add } from 'some-pkg/add.js'
import wasmUrl from 'some-pkg/add_bg.wasm?url'

await init({ module_or_path: wasmUrl })
add(1, 2)
```

不支持 `nodejs` 和 `no-modules` wasm-bindgen 目标。

## 插件选项

```ts
wasm({
  maxFileSize: 14 * 1024, // 内联的最大大小（默认：14KB）
  fileName: '[hash][extname]', // 输出文件名模式
  publicPath: '',         // 非内联文件路径的前缀
  targetEnv: 'auto',      // 'auto' | 'auto-inline' | 'browser' | 'node'
})
```

| 选项 | 默认值 | 描述 |
|--------|---------|-------------|
| `maxFileSize` | `14 * 1024` | 内联的最大文件大小。设置为 `0` 始终复制。 |
| `fileName` | `'[hash][extname]'` | 发出的 WASM 文件的模式 |
| `publicPath` | — | 非内联 WASM 文件路径的前缀 |
| `targetEnv` | `'auto'` | `'auto'` 在运行时检测；`'browser'` 省略 Node 内置模块；`'node'` 省略 fetch |

## 相关选项

- [插件](advanced-plugins.md) - 插件系统概述
- [平台](option-platform.md) - 目标平台配置