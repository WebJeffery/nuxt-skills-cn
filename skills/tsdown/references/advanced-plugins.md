# 插件

使用来自多个生态系统的插件扩展 tsdown。

## 概述

tsdown 基于 Rolldown 构建，支持来自多个生态系统的插件以扩展和自定义打包过程。

## 支持的生态系统

### 1. Rolldown 插件

为 Rolldown 设计的原生插件：

```ts
import RolldownPlugin from 'rolldown-plugin-something'

export default defineConfig({
  plugins: [RolldownPlugin()],
})
```

**兼容性：** ✅ 完全支持

### 2. Unplugin

跨打包器工作的通用插件：

```ts
import UnpluginPlugin from 'unplugin-something'

export default defineConfig({
  plugins: [UnpluginPlugin.rolldown()],
})
```

**兼容性：** ✅ 大多数 unplugin-* 插件都有效

**示例：**
- `unplugin-vue-components`
- `unplugin-auto-import`
- `unplugin-icons`

### 3. Rollup 插件

大多数 Rollup 插件都可以与 tsdown 一起使用：

```ts
import RollupPlugin from '@rollup/plugin-something'

export default defineConfig({
  plugins: [RollupPlugin()],
})
```

**兼容性：** ✅ 高兼容性

**类型问题：** 可能导致 TypeScript 错误 - 使用类型转换：

```ts
import RollupPlugin from 'rollup-plugin-something'

export default defineConfig({
  plugins: [
    // @ts-expect-error Rollup 插件类型不匹配
    RollupPlugin(),
    // 或转换为 any
    RollupPlugin() as any,
  ],
})
```

### 4. Vite 插件

某些 Vite 插件可能有效：

```ts
import VitePlugin from 'vite-plugin-something'

export default defineConfig({
  plugins: [
    // @ts-expect-error Vite 插件类型不匹配
    VitePlugin(),
  ],
})
```

**兼容性：** ⚠️ 有限 - 仅在不使用 Vite 特定 API 时

**注意：** 计划在未来的版本中改进支持。

## 使用

### 基本插件使用

```ts
import { defineConfig } from 'tsdown'
import SomePlugin from 'some-plugin'

export default defineConfig({
  entry: ['src/index.ts'],
  plugins: [SomePlugin()],
})
```

### 多个插件

```ts
import PluginA from 'plugin-a'
import PluginB from 'plugin-b'
import PluginC from 'plugin-c'

export default defineConfig({
  entry: ['src/index.ts'],
  plugins: [
    PluginA(),
    PluginB({ option: true }),
    PluginC(),
  ],
})
```

### 条件插件

```ts
export default defineConfig((options) => ({
  entry: ['src/index.ts'],
  plugins: [
    SomePlugin(),
    options.watch && DevPlugin(),
    !options.watch && ProdPlugin(),
  ].filter(Boolean),
}))
```

## 常见插件模式

### JSON 导入

```ts
import json from '@rollup/plugin-json'

export default defineConfig({
  plugins: [json()],
})
```

### Node 解析

```ts
import { nodeResolve } from '@rollup/plugin-node-resolve'

export default defineConfig({
  plugins: [nodeResolve()],
})
```

### CommonJS

```ts
import commonjs from '@rollup/plugin-commonjs'

export default defineConfig({
  plugins: [commonjs()],
})
```

### 替换

```ts
import replace from '@rollup/plugin-replace'

export default defineConfig({
  plugins: [
    replace({
      'process.env.NODE_ENV': JSON.stringify('production'),
      __VERSION__: JSON.stringify('1.0.0'),
    }),
  ],
})
```

### 自动导入

```ts
import AutoImport from 'unplugin-auto-import/rolldown'

export default defineConfig({
  plugins: [
    AutoImport({
      imports: ['vue', 'vue-router'],
      dts: 'src/auto-imports.d.ts',
    }),
  ],
})
```

### Vue 组件

```ts
import Components from 'unplugin-vue-components/rolldown'

export default defineConfig({
  plugins: [
    Components({
      dts: 'src/components.d.ts',
    }),
  ],
})
```

## 框架特定插件

### React

```ts
import react from '@vitejs/plugin-react'

export default defineConfig({
  entry: ['src/index.tsx'],
  plugins: [
    // @ts-expect-error Vite 插件
    react(),
  ],
})
```

### Vue

```ts
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  entry: ['src/index.ts'],
  plugins: [
    // @ts-expect-error Vite 插件
    vue(),
  ],
})
```

### Solid

```ts
import solid from 'vite-plugin-solid'

export default defineConfig({
  entry: ['src/index.tsx'],
  plugins: [
    // @ts-expect-error Vite 插件
    solid(),
  ],
})
```

### Svelte

```ts
import { svelte } from '@sveltejs/vite-plugin-svelte'

export default defineConfig({
  entry: ['src/index.ts'],
  plugins: [
    // @ts-expect-error Vite 插件
    svelte(),
  ],
})
```

## 编写自定义插件

遵循 Rolldown 的插件开发指南：

### 基本插件结构

```ts
import type { Plugin } from 'rolldown'

function myPlugin(): Plugin {
  return {
    name: 'my-plugin',

    // 转换钩子
    transform(code, id) {
      if (id.endsWith('.custom')) {
        return {
          code: transformCode(code),
          map: null,
        }
      }
    },

    // 其他钩子...
  }
}
```

### 使用自定义插件

```ts
import { myPlugin } from './my-plugin'

export default defineConfig({
  plugins: [myPlugin()],
})
```

## 插件配置

### 插件特定选项

请参阅每个插件的文档以获取配置选项。

### 插件顺序

插件按定义的顺序运行：

```ts
export default defineConfig({
  plugins: [
    PluginA(),  // 首先运行
    PluginB(),  // 其次运行
    PluginC(),  // 最后运行
  ],
})
```

## 故障排除

### Rollup/Vite 插件的类型错误

使用类型转换：

```ts
plugins: [
  // 选项 1：@ts-expect-error
  // @ts-expect-error 插件类型不匹配
  SomePlugin(),

  // 选项 2：as any
  SomePlugin() as any,
]
```

### 插件不工作

1. **检查兼容性** - 验证插件支持您的打包器
2. **阅读文档** - 遵循插件的设置说明
3. **检查插件顺序** - 某些插件依赖于执行顺序
4. **启用调试模式** - 使用 `--debug` 标志

### Vite 插件失败

Vite 插件可能依赖于 Vite 特定的 API：

1. **查找 Rollup 等效项** - 寻找插件的 Rollup 版本
2. **使用 Unplugin 版本** - 检查 `unplugin-*` 替代方案
3. **等待支持** - Vite 插件支持正在改进

## 资源

- [Rolldown 插件开发](https://rolldown.rs/guide/plugin-development)
- [Unplugin 文档](https://unplugin.unjs.io/)
- [Rollup 插件](https://github.com/rollup/plugins)
- [Vite 插件](https://vitejs.dev/plugins/)

## 提示

1. **首选 Rolldown 插件**以获得最佳兼容性
2. **使用 Unplugin**进行跨打包器支持
3. **为 Rollup/Vite 插件转换类型**
4. **彻底测试**当使用跨生态系统插件时
5. **检查插件文档**以获取特定配置
6. **编写自定义插件**以满足独特需求

## 相关

- [Hooks](advanced-hooks.md) - 生命周期钩子
- [Rolldown 选项](advanced-rolldown-options.md) - 高级 Rolldown 配置
- [React 配方](recipe-react.md) - 使用插件设置 React
- [Vue 配方](recipe-vue.md) - 使用插件设置 Vue
