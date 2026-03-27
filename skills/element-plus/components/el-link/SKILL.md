---
name: "el-link"
description: "用于文本超链接链接组件。当用户需要创建文本链接、导航链接样式化锚点元素时调用---

# Link 组件

Link 组件提供具有各种样式和图标支持文本超链接
## 何时使用

- 文本导航
- 外部链接
- 内联链接
- 图标链接

## 基本用法

```vue
<template>
  <el-link href="https://element-plus.org" target="_blank">default</el-link>
  <el-link type="primary">primary</el-link>
  <el-link type="success">success</el-link>
  <el-link type="warning">warning</el-link>
  <el-link type="danger">danger</el-link>
  <el-link type="info">info</el-link>
</template>
```

## 禁用

```vue
<template>
  <el-link disabled>disabled</el-link>
</template>
```

## 下划
```vue
<template>
  <el-link underline="always">Always Underlined</el-link>
  <el-link underline="hover">Underline on Hover</el-link>
  <el-link underline="never">Never Underlined</el-link>
</template>
```

## 带图
```vue
<template>
  <el-link :icon="Link">Icon Link</el-link>
  <el-link>
    <el-icon class="el-icon--left"><Link /></el-icon>
    Icon on Left
  </el-link>
</template>

<script setup>
import { Link } from '@element-plus/icons-vue'
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| type | 类型 | `'primary' \| 'success' \| 'warning' \| 'danger' \| 'info' \| 'default'` | `'default'` |
| underline | 下划线何时出| `'always' \| 'hover' \| 'never'` | `'hover'` |
| disabled | 是否禁用 | `boolean` | `false` |
| href | 原生 href 属性| `string` | |
| target | 原生 target 属性| `'_blank' \| '_parent' \| '_self' \| '_top'` | `'_self'` |
| icon | 图标组件 | `string \| Component` | |

### 插槽

| Name | Description |
|------|-------------|
| default | 自定义默认内容|
| icon | 自定义图标组件|

## 最佳实践
1. 验证 URL 以防XSS 漏洞
2. 使用 `underline="hover"` 以获得更好用户体验
3. 使用 `target="_blank"` 用于外部链接
