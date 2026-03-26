---
name: unocss-nuxt-integration
description: 用于 Nuxt 应用程序的 UnoCSS 模块
---

# UnoCSS Nuxt 集成

UnoCSS 的官方 Nuxt 模块。

## 安装

```bash
pnpm add -D unocss @unocss/nuxt
```

添加到 Nuxt 配置：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  modules: [
    '@unocss/nuxt',
  ],
})
```

创建配置文件：

```ts
// uno.config.ts
import { defineConfig, presetWind3 } from 'unocss'

export default defineConfig({
  presets: [
    presetWind3(),
  ],
})
```

**注意：** `uno.css` 入口由模块自动注入。

## 支持状态

| 构建工具 | Nuxt 2 | Nuxt Bridge | Nuxt 3 |
|------------|--------|-------------|--------|
| Webpack 开发 | ✅ | ✅ | 🚧 |
| Webpack 构建 | ✅ | ✅ | ✅ |
| Vite 开发 | - | ✅ | ✅ |
| Vite 构建 | - | ✅ | ✅ |

## 配置

### 使用 uno.config.ts（推荐）

使用专用配置文件以获得最佳 IDE 支持：

```ts
// uno.config.ts
import { defineConfig, presetWind3, presetIcons } from 'unocss'

export default defineConfig({
  presets: [
    presetWind3(),
    presetIcons(),
  ],
  shortcuts: {
    'btn': 'py-2 px-4 font-semibold rounded-lg',
  },
})
```

### Nuxt 图层支持

启用从 Nuxt 图层自动合并配置：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  unocss: {
    nuxtLayers: true,
  },
})
```

然后在根配置中：

```ts
// uno.config.ts
import config from './.nuxt/uno.config.mjs'

export default config
```

或扩展合并的配置：

```ts
// uno.config.ts
import { mergeConfigs } from '@unocss/core'
import config from './.nuxt/uno.config.mjs'

export default mergeConfigs([config, {
  // 您的覆盖
  shortcuts: {
    'custom': 'text-red-500',
  },
}])
```

## 常见设置示例

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  modules: [
    '@unocss/nuxt',
  ],
})
```

```ts
// uno.config.ts
import {
  defineConfig,
  presetAttributify,
  presetIcons,
  presetTypography,
  presetWebFonts,
  presetWind3,
  transformerDirectives,
  transformerVariantGroup,
} from 'unocss'

export default defineConfig({
  presets: [
    presetWind3(),
    presetAttributify(),
    presetIcons({
      scale: 1.2,
    }),
    presetTypography(),
    presetWebFonts({
      fonts: {
        sans: 'DM Sans',
        mono: 'DM Mono',
      },
    }),
  ],
  transformers: [
    transformerDirectives(),
    transformerVariantGroup(),
  ],
  shortcuts: [
    ['btn', 'px-4 py-1 rounded inline-block bg-teal-600 text-white cursor-pointer hover:bg-teal-700 disabled:cursor-default disabled:bg-gray-600 disabled:opacity-50'],
  ],
})
```

## 在组件中使用

```vue
<template>
  <div class="p-4 text-center">
    <h1 class="text-3xl font-bold text-blue-600">
      Hello UnoCSS!
    </h1>
    <button class="btn mt-4">
      Click me
    </button>
  </div>
</template>
```

使用 attributify 模式：

```vue
<template>
  <div p="4" text="center">
    <h1 text="3xl blue-600" font="bold">
      Hello UnoCSS!
    </h1>
  </div>
</template>
```

## 检查器

在开发中，访问 `/_nuxt/__unocss` 以访问 UnoCSS 检查器。

## 与 Vite 的主要区别

- 无需导入 `virtual:uno.css` - 自动注入
- 配置文件发现方式相同
- 所有 Vite 插件功能可用
- Nuxt 图层配置合并可用

<!--
源参考：
- https://unocss.dev/integrations/nuxt
-->
