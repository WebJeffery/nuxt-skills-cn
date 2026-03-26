# Vue 支持

使用 tsdown 构建 Vue 组件库。

## 概述

tsdown 通过集成 `unplugin-vue` 和 `rolldown-plugin-dts` 为 Vue 库提供一流的支持，用于类型生成。

## 快速开始

### 使用启动模板

```bash
npx create-tsdown@latest -t vue
```

## 基本配置

### 安装依赖

```bash
pnpm add -D unplugin-vue vue-tsc
```

### 最小设置

```ts
// tsdown.config.ts
import { defineConfig } from 'tsdown'
import Vue from 'unplugin-vue/rolldown'

export default defineConfig({
  entry: ['./src/index.ts'],
  format: ['esm', 'cjs'],
  platform: 'neutral',
  deps: {
    neverBundle: ['vue'],
  },
  plugins: [
    Vue({ isProduction: true }),
  ],
  dts: {
    vue: true,  // 启用 Vue 类型生成
  },
})
```

## 工作原理

### unplugin-vue

编译 `.vue` 单文件组件：
- 将模板转换为渲染函数
- 处理作用域样式
- 处理 script setup

### vue-tsc

生成 TypeScript 声明：
- 类型检查 Vue 组件
- 创建 `.d.ts` 文件
- 保留组件 props 类型
- 导出组件类型

## 组件示例

### 单文件组件

```vue
<!-- src/Button.vue -->
<script setup lang="ts">
interface Props {
  type?: 'primary' | 'secondary'
  disabled?: boolean
}

defineProps<Props>()
defineEmits<{
  click: []
}>()
</script>

<template>
  <button
    :class="['btn', `btn-${type}`]"
    :disabled="disabled"
    @click="$emit('click')"
  >
    <slot />
  </button>
</template>

<style scoped>
.btn {
  padding: 8px 16px;
  border-radius: 4px;
}

.btn-primary {
  background: blue;
  color: white;
}
</style>
```

### 导出组件

```ts
// src/index.ts
export { default as Button } from './Button.vue'
export { default as Input } from './Input.vue'
export { default as Modal } from './Modal.vue'

// 重新导出类型
export type { ButtonProps } from './Button.vue'
```

## 常见模式

### 组件库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  platform: 'neutral',
  deps: {
    neverBundle: ['vue'],
  },
  plugins: [
    Vue({
      isProduction: true,
      style: {
        trim: true,
      },
    }),
  ],
  dts: {
    vue: true,
  },
  clean: true,
})
```

### 多个组件

```ts
export default defineConfig({
  entry: {
    index: 'src/index.ts',
    Button: 'src/Button.vue',
    Input: 'src/Input.vue',
    Modal: 'src/Modal.vue',
  },
  format: ['esm', 'cjs'],
  deps: {
    neverBundle: ['vue'],
  },
  plugins: [Vue({ isProduction: true })],
  dts: { vue: true },
})
```

### 使用组合式工具

```ts
// src/composables/useCounter.ts
import { ref } from 'vue'

export function useCounter(initial = 0) {
  const count = ref(initial)
  const increment = () => count.value++
  const decrement = () => count.value--
  return { count, increment, decrement }
}
```

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  deps: {
    neverBundle: ['vue'],
  },
  plugins: [Vue({ isProduction: true })],
  dts: { vue: true },
})
```

### TypeScript 配置

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "jsx": "preserve",
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "strict": true,
    "isolatedDeclarations": true,
    "skipLibCheck": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

### Package.json 配置

```json
{
  "name": "my-vue-library",
  "version": "1.0.0",
  "type": "module",
  "main": "./dist/index.cjs",
  "module": "./dist/index.mjs",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "types": "./dist/index.d.ts",
      "import": "./dist/index.mjs",
      "require": "./dist/index.cjs"
    },
  },
  "files": ["dist"],
  "peerDependencies": {
    "vue": "^3.0.0"
  },
  "devDependencies": {
    "tsdown": "^0.9.0",
    "typescript": "^5.0.0",
    "unplugin-vue": "^5.0.0",
    "vue": "^3.4.0",
    "vue-tsc": "^2.0.0"
  }
}
```

## 高级模式

### 使用 Vite 插件

某些 Vite Vue 插件可能可用：

```ts
import Vue from 'unplugin-vue/rolldown'
import Components from 'unplugin-vue-components/rolldown'

export default defineConfig({
  entry: ['src/index.ts'],
  deps: {
    neverBundle: ['vue'],
  },
  plugins: [
    Vue({ isProduction: true }),
    Components({
      dts: 'src/components.d.ts',
    }),
  ],
  dts: { vue: true },
})
```

### JSX 支持

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  deps: {
    neverBundle: ['vue'],
  },
  plugins: [
    Vue({
      isProduction: true,
      script: {
        propsDestructure: true,
      },
    }),
  ],
  inputOptions: {
    transform: {
      jsx: 'automatic',
      jsxImportSource: 'vue',
    },
  },
  dts: { vue: true },
})
```

### Monorepo Vue 包

```ts
export default defineConfig({
  workspace: 'packages/*',
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  deps: {
    neverBundle: ['vue', /^@mycompany\//],
  },
  plugins: [Vue({ isProduction: true })],
  dts: { vue: true },
})
```

## 插件选项

### unplugin-vue 选项

```ts
Vue({
  isProduction: true,
  script: {
    defineModel: true,
    propsDestructure: true,
  },
  style: {
    trim: true,
  },
  template: {
    compilerOptions: {
      isCustomElement: (tag) => tag.startsWith('custom-'),
    },
  },
})
```

## 提示

1. **始终外部化 Vue** - 不要打包 Vue 本身
2. **在 dts 中启用 vue: true** - 以获得正确的类型生成
3. **使用 platform: 'neutral'** - 最大兼容性
4. **安装 vue-tsc** - 类型生成必需
5. **设置 isProduction: true** - 为生产优化
6. **添加 peer dependency** - Vue 作为 peer dependency

## 故障排除

### 类型生成失败

确保安装了 vue-tsc：
```bash
pnpm add -D vue-tsc
```

在配置中启用：
```ts
dts: { vue: true }
```

### 组件类型缺失

检查 TypeScript 配置：
```json
{
  "compilerOptions": {
    "jsx": "preserve",
    "moduleResolution": "bundler"
  }
}
```

### Vue 未外部化

添加到 deps.neverBundle：
```ts
deps: {
  neverBundle: ['vue'],
}
```

### SFC 编译错误

检查 unplugin-vue 版本：
```bash
pnpm add -D unplugin-vue@latest
```

## 相关内容

- [插件](advanced-plugins.md) - 插件系统
- [依赖](option-dependencies.md) - 外部包
- [DTS](option-dts.md) - 类型声明
- [React 配方](recipe-react.md) - React 组件库