---
name: "el-breadcrumb"
description: "用于显示当前页面位置面包屑组件。当用户需要显示导航路径、页面层次结构启用更轻松返回浏览时调用
---

# Breadcrumb 组件

Breadcrumb 组件显示当前页面位使用户更容易通过导航层次结构返回浏览
## 何时使用

- 页面导航路径
- 类别层次结构
- 文档结构
- 多级导航

## 基本用法

```vue
<template>
  <el-breadcrumb separator="/">
    <el-breadcrumb-item :to="{ path: '/' }">homepage</el-breadcrumb-item>
    <el-breadcrumb-item><a href="/">promotion management</a></el-breadcrumb-item>
    <el-breadcrumb-item>promotion list</el-breadcrumb-item>
    <el-breadcrumb-item>promotion detail</el-breadcrumb-item>
  </el-breadcrumb>
</template>
```

## 图标分隔
```vue
<template>
  <el-breadcrumb :separator-icon="ArrowRight">
    <el-breadcrumb-item :to="{ path: '/' }">homepage</el-breadcrumb-item>
    <el-breadcrumb-item>promotion management</el-breadcrumb-item>
    <el-breadcrumb-item>promotion list</el-breadcrumb-item>
  </el-breadcrumb>
</template>

<script setup>
import { ArrowRight } from '@element-plus/icons-vue'
</script>
```

## API 参考
### Breadcrumb 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| separator | 分隔符字| `string` | `'/'` |
| separator-icon | 分隔符图标组件 | `string \| Component` | |

### Breadcrumb 插槽

| Name | Description | Subtags |
|------|-------------|---------|
| default | 自定义默认内容| Breadcrumb Item |

### BreadcrumbItem 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| to | 目标路由,vue-router `to` 相同 | `string \| RouteLocationRaw` | `''` |
| replace | 如果true,导航不会留下历史记录 | `boolean` | `false` |

### BreadcrumbItem 插槽

| Name | Description |
|------|-------------|
| default | 自定义默认内容|

## 常见模式

### 动态面包屑

```vue
<template>
  <el-breadcrumb separator="/">
    <el-breadcrumb-item v-for="item in breadcrumbs" :key="item.path" :to="item.path">
      {{ item.title }}
    </el-breadcrumb-item>
  </el-breadcrumb>
</template>

<script setup>
import { computed } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()

const breadcrumbs = computed(() => {
  return route.matched.filter(r => r.meta?.title).map(r => ({
    path: r.path,
    title: r.meta.title
  }))
})
</script>
```

## 最佳实践
1. 使用 `to` 属性进vue-router 集成
2. 使用 `separator-icon` 进行自定义分隔符
3. 保持面包屑路径简洁且有意