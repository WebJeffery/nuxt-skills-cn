---
name: "el-text"
description: "用于文本显示文本组件带有样式选项。当用户需要显示样式化文本、截断文本带有语义类型文本时调用---

# Text 组件

Text 组件提供具有各种选项样式化文本显示
## 何时使用

- 样式化文本显- 截断文本
- 语义文本类型
- 带有尺寸文
## 基本用法

```vue
<template>
  <el-text>Default text</el-text>
  <el-text type="primary">Primary text</el-text>
  <el-text type="success">Success text</el-text>
  <el-text type="warning">Warning text</el-text>
  <el-text type="danger">Danger text</el-text>
  <el-text type="info">Info text</el-text>
</template>
```

## 尺寸

```vue
<template>
  <el-text size="large">Large text</el-text>
  <el-text>Default text</el-text>
  <el-text size="small">Small text</el-text>
</template>
```

## 截断

```vue
<template>
  <el-text truncated>
    This is a very long text that will be truncated with ellipsis when it exceeds container width
  </el-text>
</template>
```

## 行限
```vue
<template>
  <el-text :line-clamp="2">
    This is a very long text that will be clamped to 2 lines with ellipsis when it exceeds container width. This is a very long text that will be clamped to 2 lines with ellipsis when it exceeds container width.
  </el-text>
</template>
```

## 覆盖标签

```vue
<template>
  <el-text tag="p">Paragraph text</el-text>
  <el-text tag="div">Div text</el-text>
</template>
```

## 混合

```vue
<template>
  <el-text>
    Normal text with
    <el-text type="primary">primary</el-text>
    and
    <el-text type="danger">danger</el-text>
    inline
  </el-text>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| type | 文本类型 | `'primary' \| 'success' \| 'warning' \| 'danger' \| 'info'` | |
| size | 文本尺寸 | `'large' \| 'default' \| 'small'` | `'default'` |
| truncated | 渲染省略| `boolean` | `false` |
| line-clamp | 最大行| `string \| number` | |
| tag | 自定义元素标签| `string` | `'span'` |

### 插槽

| Name | Description |
|------|-------------|
| default | 默认内容 |

## 最佳实践
1. 使用 `truncated` 用于单行省略2. 使用 `line-clamp` 用于多行省略3. 使用 `tag` 用于语义HTML
