---
name: "el-anchor"
description: "用于锚点导航锚点组件。当用户需要创建页面锚点导航、目录快速导航到特定部分时调用
---

# Anchor 组件

Anchor 提供快速导航到当前页面特定部分
## 何时使用

- 页面目录
- 文档导航
- 部分锚点
- 快速页面导
## 基本用法

```vue
<template>
  <el-anchor>
    <el-anchor-link href="#section1" title="Section 1" />
    <el-anchor-link href="#section2" title="Section 2" />
    <el-anchor-link href="#section3" title="Section 3" />
  </el-anchor>
</template>
```

## 水平模式

```vue
<template>
  <el-anchor direction="horizontal">
    <el-anchor-link href="#section1" title="Section 1" />
    <el-anchor-link href="#section2" title="Section 2" />
  </el-anchor>
</template>
```

## 滚动容器

```vue
<template>
  <el-anchor :container="containerRef" :offset="100">
    <el-anchor-link href="#section1" title="Section 1" />
  </el-anchor>
</template>

<script setup>
import { ref } from 'vue'

const containerRef = ref(null)
</script>
```

## 下划线类型
```vue
<template>
  <el-anchor type="underline">
    <el-anchor-link href="#section1" title="Section 1" />
  </el-anchor>
</template>
```

## API 参考
### Anchor 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| container | 滚动容器 | `string \| HTMLElement \| Window` | |
| offset | 滚动偏移 | `number` | `0` |
| bound | 触发锚点偏| `number` | `15` |
| duration | 滚动持续时间(毫秒) | `number` | `300` |
| marker | 显示标记 | `boolean` | `true` |
| type | 锚点类型 | `'default' \| 'underline'` | `'default'` |
| direction | 锚点方向 | `'vertical' \| 'horizontal'` | `'vertical'` |

### Anchor 事件

| Name | Description | Type |
|------|-------------|------|
| change | 激活链接改| `(href: string) => void` |
| click | 链接点击 | `(e: MouseEvent, href?: string) => void` |

### Anchor 暴露

| Name | Description | Type |
|------|-------------|------|
| scrollTo | 滚动到特定位| `(href: string) => void` |

### AnchorLink 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| title | 链接文本 | `string` | |
| href | 链接地址 | `string` | |

### AnchorLink 插槽

| Name | Description |
|------|-------------|
| default | 子链|
| sub-link | 子链|

## 最佳实践
1. 使用 `container` 用于自定义滚动区2. 使用 `offset` 用于固定页眉
3. 使用 `direction="horizontal"` 用于顶部导航
