---
name: "el-watermark"
description: "用于添加文本图案水印水印组件。当用户需要保护内容、品牌化文档添加版权声明时调用---

# Watermark 组件

Watermark 向页面添加特定文本图案
## 何时使用

- 内容保护
- 品牌- 版权声明
- 文档安全

## 基本用法

```vue
<template>
  <el-watermark content="Element Plus">
    <div style="height: 500px">
      Content goes here
    </div>
  </el-watermark>
</template>
```

## 多行水印

```vue
<template>
  <el-watermark :content="['Element Plus', 'Vue 3']">
    <div style="height: 500px">
      Content goes here
    </div>
  </el-watermark>
</template>
```

## 图像水印

```vue
<template>
  <el-watermark
    image="https://element-plus.org/images/element-plus-logo-small.svg"
    :width="130"
    :height="50"
  >
    <div style="height: 500px">
      Content goes here
    </div>
  </el-watermark>
</template>
```

## 自定义配合
```vue
<template>
  <el-watermark
    content="Confidential"
    :font="font"
    :gap="[100, 100]"
    :rotate="-22"
  >
    <div style="height: 500px">
      Content goes here
    </div>
  </el-watermark>
</template>

<script setup>
const font = {
  color: 'rgba(0, 0, 0, 0.15)',
  fontSize: 16,
  fontWeight: 'normal',
  fontFamily: 'sans-serif'
}
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| width | 水印宽度 | `number` | `120` |
| height | 水印高度 | `number` | `64` |
| rotate | 旋转角度( | `number` | `-22` |
| z-index | 水印z-index | `number` | `9` |
| image | 图像| `string` | |
| content | 水印文本 | `string \| string[]` | `'Element Plus'` |
| font | 文本样式 | `Font` | |
| gap | 水印之间间| `[number, number]` | `[100, 100]` |
| offset | 从左上角偏| `[number, number]` | `[gap[0]/2, gap[1]/2]` |

### 字体配置

| Name | Description | Type | Default |
|------|-------------|------|---------|
| color | 字体颜色 | `string` | `'rgba(0,0,0,.15)'` |
| fontSize | 字体大小 | `number \| string` | `16` |
| fontWeight | 字体粗细 | `'normal' \| 'bold' \| number` | `'normal'` |
| fontFamily | 字体系列 | `string` | `'sans-serif'` |
| fontStyle | 字体样式 | `'none' \| 'normal' \| 'italic' \| 'oblique'` | `'normal'` |
| textAlign | 文本对齐 | `'left' \| 'right' \| 'center' \| 'start' \| 'end'` | `'center'` |

### 插槽

| Name | Description |
|------|-------------|
| default | 水印容器 |

## 最佳实践
1. 使用 `image` 用于品牌徽标
2. 设置适当`gap` 用于覆盖
3. 使用低不透明度 `font.color` 以获得微妙效