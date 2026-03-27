---
name: "el-image"
description: "具有懒加载、占位符和预览功能图片组件。当用户需要显示带有加载状态、错误处理预览功能图片时调用---

# Image 组件

Image 组件扩展了原img,具有懒加载、占位符、错误处理和预览功能
## 何时使用

- 带有懒加载图片显示
- 图片预览- 加载占位- 错误回退图片

## 基本用法

```vue
<template>
  <el-image style="width: 100px; height: 100px" :src="url" fit="cover" />
</template>

<script setup>
const url = 'https://example.com/image.jpg'
</script>
```

## 占位
```vue
<template>
  <el-image :src="url" fit="cover">
    <template #placeholder>
      <div class="image-slot">Loading...</div>
    </template>
  </el-image>
</template>
```

## 加载失败

```vue
<template>
  <el-image :src="invalidUrl">
    <template #error>
      <div class="image-slot">
        <el-icon><icon-picture /></el-icon>
      </div>
    </template>
  </el-image>
</template>
```

## 懒加
```vue
<template>
  <el-image :src="url" lazy />
</template>
```

## 图片预览

```vue
<template>
  <el-image
    style="width: 100px; height: 100px"
    :src="url"
    :preview-src-list="srcList"
    :initial-index="0"
    fit="cover"
  />
</template>

<script setup>
const url = 'https://example.com/image1.jpg'
const srcList = [
  'https://example.com/image1.jpg',
  'https://example.com/image2.jpg',
  'https://example.com/image3.jpg'
]
</script>
```

## API 参考
### Image 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| src | 图片| `string` | `''` |
| fit | 图片如何适应容器 | `'fill' \| 'contain' \| 'cover' \| 'none' \| 'scale-down'` | `''` |
| hide-on-click-modal | 点击背景时隐| `boolean` | `false` |
| loading | 原生 loading 属性| `'eager' \| 'lazy'` | |
| lazy | 启用懒加| `boolean` | `false` |
| scroll-container | 懒加载滚动容器 | `string \| HTMLElement` | |
| alt | 原生 alt 属性| `string` | |
| preview-src-list | 预览图片列表 | `string[]` | `[]` |
| z-index | 预览 z-index | `number` | |
| initial-index | 初始预览索引 | `number` | `0` |
| close-on-press-escape | ESC 关闭 | `boolean` | `true` |
| preview-teleported | 将预览传送到 body | `boolean` | `false` |
| infinite | 无限预览 | `boolean` | `true` |
| zoom-rate | 缩放| `number` | `1.2` |
| min-scale | 最小缩| `number` | `0.2` |
| max-scale | 最大缩| `number` | `7` |
| show-progress | 显示进度 | `boolean` | `false` |

### Image 事件

| Name | Description | Type |
|------|-------------|------|
| load | 与原load 相同 | `(e: Event) => void` |
| error | 与原error 相同 | `(e: Event) => void` |
| switch | 切换图片时触| `(index: number) => void` |
| close | 关闭预览时触| `() => void` |
| show | 查看器显示时触发 | `() => void` |

### Image 插槽

| Name | Description |
|------|-------------|
| placeholder | 自定义占位符内容 |
| error | 自定义错误内容|

### Image 暴露

| Name | Description | Type |
|------|-------------|------|
| showPreview | 手动打开预览 | `() => void` |

## 最佳实践
1. 使用 `lazy` 用于折叠下方图2. 为加载状态提`placeholder`
3. 使用 `preview-src-list` 用于图片