---
name: "el-splitter"
description: "用于水平或垂直分割区域的分割器组件。当用户需要创建可调整大小的面板、分割视图或可折叠布局时调用。"
---

# Splitter 组件

Splitter 通过可拖动调整大小来水平或垂直分割区域。

## 何时使用

- 分割视图
- 可调整大小的面板
- 可折叠布局
- 多窗格界面

## 基本用法

```vue
<template>
  <el-splitter>
    <el-splitter-panel>面板 1</el-splitter-panel>
    <el-splitter-panel>面板 2</el-splitter-panel>
  </el-splitter>
</template>
```

## 垂直布局

```vue
<template>
  <el-splitter layout="vertical">
    <el-splitter-panel>面板 1</el-splitter-panel>
    <el-splitter-panel>面板 2</el-splitter-panel>
  </el-splitter>
</template>
```

## 可折叠

```vue
<template>
  <el-splitter>
    <el-splitter-panel collapsible>面板 1</el-splitter-panel>
    <el-splitter-panel>面板 2</el-splitter-panel>
  </el-splitter>
</template>
```

## 大小限制

```vue
<template>
  <el-splitter>
    <el-splitter-panel :min="100" :max="300">面板 1</el-splitter-panel>
    <el-splitter-panel>面板 2</el-splitter-panel>
  </el-splitter>
</template>
```

## 禁用拖动

```vue
<template>
  <el-splitter>
    <el-splitter-panel :resizable="false">面板 1</el-splitter-panel>
    <el-splitter-panel>面板 2</el-splitter-panel>
  </el-splitter>
</template>
```

## 跟踪大小

```vue
<template>
  <el-splitter>
    <el-splitter-panel v-model:size="size1">面板 1</el-splitter-panel>
    <el-splitter-panel v-model:size="size2">面板 2</el-splitter-panel>
  </el-splitter>
</template>

<script setup>
import { ref } from 'vue'

const size1 = ref('50%')
const size2 = ref('50%')
</script>
```

## 懒加载模式

```vue
<template>
  <el-splitter lazy>
    <el-splitter-panel>面板 1</el-splitter-panel>
    <el-splitter-panel>面板 2</el-splitter-panel>
  </el-splitter>
</template>
```

## API 参考

### Splitter 属性

| Name | Description | Type | Default |
|------|-------------|------|---------|
| layout | 布局方向 | `'horizontal' \| 'vertical'` | `'horizontal'` |
| lazy | 启用懒加载模式 | `boolean` | `false` |

### Splitter 事件

| Name | Description | Type |
|------|-------------|------|
| resize-start | 调整大小开始 | `(index, sizes) => void` |
| resize | 调整大小期间 | `(index, sizes) => void` |
| resize-end | 调整大小结束 | `(index, sizes) => void` |
| collapse | 面板折叠 | `(index, type, sizes) => void` |

### SplitterPanel 属性

| Name | Description | Type | Default |
|------|-------------|------|---------|
| size / v-model:size | 面板大小 | `string \| number` | - |
| min | 最小大小 | `string \| number` | - |
| max | 最大大小 | `string \| number` | - |
| resizable | 可调整大小 | `boolean` | `true` |
| collapsible | 可折叠 | `boolean` | `false` |

### SplitterPanel 事件

| Name | Description | Type |
|------|-------------|------|
| update:size | 大小变化 | `(size: number) => void` |

### SplitterPanel 插槽

| Name | Description |
|------|-------------|
| default | 面板内容 |
| start-collapsible | 开始折叠按钮 |
| end-collapsible | 结束折叠按钮 |

## 最佳实践

1. 使用 `min`/`max` 限制面板大小
2. 使用 `collapsible` 用于可折叠面板
3. 使用 `lazy` 用于繁重内容的性能优化
