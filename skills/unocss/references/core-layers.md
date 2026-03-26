---
name: unocss-layers-preflights
description: CSS 图层排序和原始 CSS 注入
---

# 图层和预置

控制 CSS 输出顺序并注入全局 CSS。

## 图层

在规则上设置图层：

```ts
rules: [
  [/^m-(\d)$/, ([, d]) => ({ margin: `${d / 4}rem` }), { layer: 'utilities' }],
  ['btn', { padding: '4px' }], // 默认图层
]
```

### 图层排序

```ts
layers: {
  'components': -1,
  'default': 1,
  'utilities': 2,
}
```

### 单独导入图层

```ts
import 'uno:components.css'
import 'uno.css'
import './my-custom.css'
import 'uno:utilities.css'
```

### CSS 级联图层

```ts
outputToCssLayers: true

// 或使用自定义名称
outputToCssLayers: {
  cssLayerName: (layer) => {
    if (layer === 'default') return 'utilities'
    if (layer === 'shortcuts') return 'utilities.shortcuts'
  }
}
```

## 图层变体

```html
<!-- UnoCSS 图层 -->
<p class="uno-layer-my-layer:text-xl">

<!-- CSS @layer -->
<p class="layer-my-layer:text-xl">
```

## 预置

全局注入原始 CSS：

```ts
preflights: [
  {
    getCSS: ({ theme }) => `
      * {
        color: ${theme.colors.gray?.[700] ?? '#333'};
        margin: 0;
      }
    `,
  },
]
```

带图层：

```ts
preflights: [
  {
    layer: 'base',
    getCSS: () => `html { font-family: system-ui; }`,
  },
]
```

## preset-wind4 图层

| 图层 | 描述 | 顺序 |
|-------|-------------|-------|
| `properties` | CSS @property 规则 | -200 |
| `theme` | 主题 CSS 变量 | -150 |
| `base` | 重置样式 | -100 |

<!--
源参考：
- https://unocss.dev/config/layers
- https://unocss.dev/config/preflights
-->
