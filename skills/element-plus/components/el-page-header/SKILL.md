---
name: "el-page-header"
description: "用于页面导航页眉页眉组件。当用户需要创建带有返回导航、面包屑标题部分页面页眉时调用---

# PageHeader 组件

PageHeader 提供一个带有返回导航和内容区域页眉部分
## 何时使用

- 页面页眉
- 返回导航
- 面包屑集- 页面标题部分

## 基本用法

```vue
<template>
  <el-page-header @back="goBack">
    <template #content>
      <span class="text-large font-600 mr-3"> Title </span>
    </template>
  </el-page-header>
</template>

<script setup>
const goBack = () => {
  console.log('Go back')
}
</script>
```

## 完整示例

```vue
<template>
  <el-page-header @back="goBack">
    <template #breadcrumb>
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }">Home</el-breadcrumb-item>
        <el-breadcrumb-item>Detail</el-breadcrumb-item>
      </el-breadcrumb>
    </template>
    <template #content>
      <span class="text-large font-600 mr-3"> Title </span>
    </template>
    <template #extra>
      <el-button type="primary">Action</el-button>
    </template>
    <template #default>
      <p>Description content</p>
    </template>
  </el-page-header>
</template>
```

## 自定义图
```vue
<template>
  <el-page-header :icon="ArrowLeft" @back="goBack">
    <template #content>
      <span>Custom Icon</span>
    </template>
  </el-page-header>
</template>

<script setup>
import { ArrowLeft } from '@element-plus/icons-vue'
</script>
```

## 无图
```vue
<template>
  <el-page-header icon="" @back="goBack">
    <template #content>
      <span>No Icon</span>
    </template>
  </el-page-header>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| icon | 图标组件 | `string \| Component` | `Back` |
| title | 主标签| `string` | `''` |
| content | 内容文本 | `string` | `''` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| back | 返回按钮点击 | `() => void` |

### 插槽

| Name | Description |
|------|-------------|
| icon | 自定义图|
| title | 自定义标签|
| content | 自定义内容|
| extra | 额外部分 |
| breadcrumb | 面包屑部|
| default | 主内容|

## 最佳实践
1. 使用 `breadcrumb` 插槽进行导航路径
2. 使用 `extra` 插槽进行操作按钮
3. 使用 `default` 插槽进行页面内容
