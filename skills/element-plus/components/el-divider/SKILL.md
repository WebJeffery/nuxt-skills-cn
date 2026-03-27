---
name: "el-divider"
description: "用于分隔内容分割线组件。当用户需要在内容部分之间创建视觉分隔、添加水平垂直分割线时调用---

# Divider 组件

Divider 组件创建一条分隔线来分隔内容
## 何时使用

- 内容分隔
- 部分分隔- 视觉分组
- 布局组织

## 基本用法

```vue
<template>
  <div>
    <p>Content above</p>
    <el-divider />
    <p>Content below</p>
  </div>
</template>
```

## 自定义内容
```vue
<template>
  <el-divider content-position="left">Left</el-divider>
  <el-divider content-position="center">Center</el-divider>
  <el-divider content-position="right">Right</el-divider>
</template>
```

## 虚线

```vue
<template>
  <el-divider border-style="dashed" />
</template>
```

## 垂直分割
```vue
<template>
  <div style="display: flex; align-items: center;">
    <span>Left</span>
    <el-divider direction="vertical" />
    <span>Right</span>
  </div>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| direction | 分割线方| `'horizontal' \| 'vertical'` | `'horizontal'` |
| border-style | 分割线样式| CSS border-style | `'solid'` |
| content-position | 内容位| `'left' \| 'right' \| 'center'` | `'center'` |

### 插槽

| Name | Description |
|------|-------------|
| default | 分割线上自定义内容 |

## 最佳实践
1. 使用 `direction="vertical"` 用于内联分隔2. 使用 `border-style="dashed"` 用于微妙分隔线
3. 使用 `content-position` 定位内容
