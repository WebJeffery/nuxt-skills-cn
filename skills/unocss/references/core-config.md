---
name: unocss-configuration
description: UnoCSS 的配置文件设置和所有配置选项
---

# UnoCSS 配置

UnoCSS 通过项目根目录中的专用配置文件进行配置。

## 配置文件

**推荐：** 使用专用的 `uno.config.ts` 文件以获得最佳的 IDE 支持和 HMR。

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
  transformerVariantGroup
} from 'unocss'

export default defineConfig({
  shortcuts: [
    // ...
  ],
  theme: {
    colors: {
      // ...
    }
  },
  presets: [
    presetWind3(),
    presetAttributify(),
    presetIcons(),
    presetTypography(),
    presetWebFonts({
      fonts: {
        // ...
      },
    }),
  ],
  transformers: [
    transformerDirectives(),
    transformerVariantGroup(),
  ],
})
```

UnoCSS 会自动在项目根目录中查找 `uno.config.{js,ts,mjs,mts}` 或 `unocss.config.{js,ts,mjs,mts}`。

## 主要配置选项

### rules
定义 CSS 工具规则。后面的条目具有更高的优先级。

```ts
rules: [
  ['m-1', { margin: '0.25rem' }],
  [/^m-(\d+)$/, ([, d]) => ({ margin: `${d / 4}rem` })],
]
```

### shortcuts
将多个规则组合成单个简写。

```ts
shortcuts: {
  'btn': 'py-2 px-4 font-semibold rounded-lg shadow-md',
}
```

### theme
在规则之间共享的设计令牌的主题对象。

```ts
theme: {
  colors: {
    brand: '#942192',
  },
  breakpoints: {
    sm: '640px',
    md: '768px',
  },
}
```

### presets
捆绑规则、变体和主题的预定义配置。

```ts
presets: [
  presetWind3(),
  presetIcons(),
]
```

### transformers
转换源代码以支持特殊语法。

```ts
transformers: [
  transformerDirectives(),
  transformerVariantGroup(),
]
```

### variants
使用重写 CSS 输出的能力预处理选择器。

### extractors
处理源文件并提取工具类名称。

### preflights
全局注入原始 CSS。

### layers
控制 CSS 图层的顺序。默认为 `0`。

```ts
layers: {
  'components': -1,
  'default': 1,
  'utilities': 2,
}
```

### safelist
始终包含在输出中的工具类。

```ts
safelist: ['p-1', 'p-2', 'p-3']
```

### blocklist
始终排除的工具类。

```ts
blocklist: ['p-1', /^p-[2-4]$/]
```

### content
配置从何处提取工具类。

```ts
content: {
  pipeline: {
    include: [/\.(vue|svelte|tsx|html)($|\?)/],
  },
  filesystem: ['src/**/*.php'],
}
```

### separators
变体分隔符。默认为 `[':', '-']`

### outputToCssLayers
将 UnoCSS 图层输出为 CSS 级联图层。

```ts
outputToCssLayers: true
```

## 指定配置文件位置

```ts
// vite.config.ts
import UnoCSS from 'unocss/vite'

export default defineConfig({
  plugins: [
    UnoCSS({
      configFile: '../my-uno.config.ts',
    }),
  ],
})
```

<!--
源参考：
- https://unocss.dev/guide/config-file
- https://unocss.dev/config/
-->
