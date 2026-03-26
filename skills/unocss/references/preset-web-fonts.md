---
name: preset-web-fonts
description: 轻松集成 Google Fonts 和其他 Web 字体
---

# 预设 Web Fonts

轻松使用来自 Google Fonts 和其他提供商的 Web 字体。

## 安装

```ts
import { defineConfig, presetWebFonts, presetWind3 } from 'unocss'

export default defineConfig({
  presets: [
    presetWind3(),
    presetWebFonts({
      provider: 'google',
      fonts: {
        sans: 'Roboto',
        mono: 'Fira Code',
      },
    }),
  ],
})
```

## 提供商

- `google` - Google Fonts（默认）
- `bunny` - 隐私友好的替代方案
- `fontshare` - ITF 提供的优质字体
- `fontsource` - 自托管开源字体
- `coollabs` - 隐私友好的替代品
- `none` - 视为系统字体

## 字体配置

```ts
fonts: {
  // 简单
  sans: 'Roboto',
  
  // 多个（回退）
  mono: ['Fira Code', 'Fira Mono:400,700'],
  
  // 详细
  lato: [
    {
      name: 'Lato',
      weights: ['400', '700'],
      italic: true,
    },
    {
      name: 'sans-serif',
      provider: 'none',
    },
  ],
}
```

## 用法

```html
<p class="font-sans">Roboto</p>
<code class="font-mono">Fira Code</code>
```

## 本地字体

自托管字体：

```ts
import { createLocalFontProcessor } from '@unocss/preset-web-fonts/local'

presetWebFonts({
  provider: 'none',
  fonts: { sans: 'Roboto' },
  processors: createLocalFontProcessor({
    cacheDir: 'node_modules/.cache/unocss/fonts',
    fontAssetsDir: 'public/assets/fonts',
    fontServeBaseUrl: '/assets/fonts',
  })
})
```

<!--
源参考：
- https://unocss.dev/presets/web-fonts
-->
