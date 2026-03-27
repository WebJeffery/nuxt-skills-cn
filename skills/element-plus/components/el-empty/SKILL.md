---
name: "el-empty"
description: "用于空状态占位符提示空状态组件。当用户需要显示空数据状态、未找到结果占位符内容时调用---

# Empty 组件

Empty 组件为空状态提供占位符提示,具有可自定义图像和内容
## 何时使用

- 空数据状态- 无搜索结- 占位符内容- 错误状态
## 基本用法

```vue
<template>
  <el-empty />
</template>
```

## 自定义图
```vue
<template>
  <el-empty image="https://example.com/empty.png" />
</template>
```

## 图像大小

```vue
<template>
  <el-empty :image-size="200" />
</template>
```

## 底部内容

```vue
<template>
  <el-empty>
    <el-button type="primary">Button</el-button>
  </el-empty>
</template>
```

## 自定义描
```vue
<template>
  <el-empty description="No data found">
    <el-button type="primary">Add Data</el-button>
  </el-empty>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| image | 图像 URL | `string` | `''` |
| image-size | 图像大小(宽度) | `number` | |
| description | 描述文本 | `string` | `''` |

### 插槽

| Name | Description |
|------|-------------|
| default | 内容作为底部内容 |
| image | 内容作为图像 |
| description | 内容作为描述 |

## 常见模式

### 空表格状态
```vue
<template>
  <el-table :data="tableData">
    <el-table-column prop="name" label="Name" />
    <template #empty>
      <el-empty description="No data available">
        <el-button type="primary" @click="fetchData">Refresh</el-button>
      </el-empty>
    </template>
  </el-table>
</template>
```

### 自定SVG 图像

```vue
<template>
  <el-empty>
    <template #image>
      <svg viewBox="0 0 100 100">
        <!-- Custom SVG -->
      </svg>
    </template>
  </el-empty>
</template>
```

## 最佳实践
1. 在空状态下提供可操作按钮2. 使用描述性消3. 根据上下文自定义图像大小
