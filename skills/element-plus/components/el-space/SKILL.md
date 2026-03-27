---
name: "el-space"
description: "用于元素之间一致间距间距组件。当用户需要在组件之间创建一致间隙、布局间距元素排列时调用---

# Space 组件

Space 组件在元素之间提供一致间距,以便更好地控制布局
## 何时使用

- 一致组件间距
- 按钮- 表单布局
- 导航项目

## 基本用法

```vue
<template>
  <el-space>
    <el-button>Button 1</el-button>
    <el-button>Button 2</el-button>
    <el-button>Button 3</el-button>
  </el-space>
</template>
```

## 垂直布局

```vue
<template>
  <el-space direction="vertical">
    <el-button>Button 1</el-button>
    <el-button>Button 2</el-button>
  </el-space>
</template>
```

## 控制尺寸

```vue
<template>
  <el-space size="small">
    <el-button>Small</el-button>
  </el-space>
  <el-space size="default">
    <el-button>Default</el-button>
  </el-space>
  <el-space size="large">
    <el-button>Large</el-button>
  </el-space>
</template>
```

## 自定义尺寸
```vue
<template>
  <el-space :size="20">
    <el-button>Button 1</el-button>
    <el-button>Button 2</el-button>
  </el-space>
</template>
```

## 自动换行

```vue
<template>
  <el-space wrap>
    <el-button v-for="i in 20" :key="i">Button {{ i }}</el-button>
  </el-space>
</template>
```

## 分隔
```vue
<template>
  <el-space spacer="|">
    <el-button>Button 1</el-button>
    <el-button>Button 2</el-button>
  </el-space>
</template>
```

## 填充容器

```vue
<template>
  <el-space fill>
    <el-button>Button 1</el-button>
    <el-button>Button 2</el-button>
  </el-space>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| alignment | 项目对齐方| CSS align-items | `'center'` |
| direction | 放置方向 | `'vertical' \| 'horizontal'` | `'horizontal'` |
| spacer | 分隔符元| `string \| number \| VNode` | |
| size | 间距尺寸 | `'default' \| 'small' \| 'large' \| number \| [number, number]` | `'small'` |
| wrap | 自动换行 | `boolean` | `false` |
| fill | 填充容器 | `boolean` | `false` |
| fill-ratio | 填充比例 | `number` | `100` |

### 插槽

| Name | Description |
|------|-------------|
| default | 要间隔项目 |

## 最佳实践
1. 使用 `wrap` 用于响应式布局
2. 使用 `spacer` 用于自定义分隔符
3. 使用 `fill` 用于等宽项目
