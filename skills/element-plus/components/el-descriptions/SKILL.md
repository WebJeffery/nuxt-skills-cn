---
name: "el-descriptions"
description: "用于以列表形式显示多个字段描述列表组件。当用户需要显示详细信息、产品规格个人资料数据时调用---

# Descriptions 组件

Descriptions 组件以列表形式显示多个字适用于详情页和信息展示
## 何时使用

- 产品详情- 用户资料展示
- 信息摘要
- 规格列表

## 基本用法

```vue
<template>
  <el-descriptions title="User Info">
    <el-descriptions-item label="Username">kooriookami</el-descriptions-item>
    <el-descriptions-item label="Telephone">18100000000</el-descriptions-item>
    <el-descriptions-item label="Place">Suzhou</el-descriptions-item>
    <el-descriptions-item label="Remarks">
      <el-tag size="small">School</el-tag>
    </el-descriptions-item>
  </el-descriptions>
</template>
```

## 尺寸

```vue
<template>
  <el-descriptions title="Large" size="large">
    <el-descriptions-item label="Username">kooriookami</el-descriptions-item>
  </el-descriptions>
  
  <el-descriptions title="Default">
    <el-descriptions-item label="Username">kooriookami</el-descriptions-item>
  </el-descriptions>
  
  <el-descriptions title="Small" size="small">
    <el-descriptions-item label="Username">kooriookami</el-descriptions-item>
  </el-descriptions>
</template>
```

## 垂直列表

```vue
<template>
  <el-descriptions direction="vertical" border>
    <el-descriptions-item label="Username">kooriookami</el-descriptions-item>
    <el-descriptions-item label="Telephone">18100000000</el-descriptions-item>
    <el-descriptions-item label="Place">Suzhou</el-descriptions-item>
  </el-descriptions>
</template>
```

## 带边
```vue
<template>
  <el-descriptions border>
    <el-descriptions-item label="Username">kooriookami</el-descriptions-item>
    <el-descriptions-item label="Telephone">18100000000</el-descriptions-item>
  </el-descriptions>
</template>
```

## 行跨
```vue
<template>
  <el-descriptions border direction="vertical">
    <el-descriptions-item label="Username">kooriookami</el-descriptions-item>
    <el-descriptions-item label="Telephone" :rowspan="2">18100000000</el-descriptions-item>
    <el-descriptions-item label="Place">Suzhou</el-descriptions-item>
    <el-descriptions-item label="Address">No.1188, Wuzhong Avenue</el-descriptions-item>
  </el-descriptions>
</template>
```

## API 参考
### Descriptions 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| border | 是否带边| `boolean` | `false` |
| column | 一行项目| `number` | `3` |
| direction | 列表方向 | `'vertical' \| 'horizontal'` | `'horizontal'` |
| size | 列表尺寸 | `'' \| 'large' \| 'default' \| 'small'` | |
| title | 标题文本 | `string` | `''` |
| extra | 右上角额外文| `string` | `''` |
| label-width | 每列标签宽| `string \| number` | |

### Descriptions 插槽

| Name | Description | Subtags |
|------|-------------|---------|
| default | 自定义默认内容| Descriptions Item |
| title | 自定义标签| |
| extra | 自定义额外区| |

### DescriptionsItem 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| label | 标签文本 | `string` | `''` |
| span | 列跨| `number` | `1` |
| rowspan | 行跨| `number` | `1` |
| width | 列宽| `string \| number` | `''` |
| min-width | 列最小宽| `string \| number` | `''` |
| label-width | 列标签宽| `string \| number` | |
| align | 列内容对齐方| `'left' \| 'center' \| 'right'` | `'left'` |
| label-align | 列标签对齐方| `'left' \| 'center' \| 'right'` | |
| class-name | 列内容类型| `string` | `''` |
| label-class-name | 列标签类型| `string` | `''` |

## 最佳实践
1. 使用 `border` 获得更好视觉分2. 使用 `direction="vertical"` 用于紧凑显示
3. 使用 `span` `rowspan` 用于复杂布局
