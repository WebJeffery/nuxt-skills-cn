---
name: "el-card"
description: "用于将信息集成在卡片容器中卡片组件。当用户需要显示分组内容、仪表板卡片带标题内容区域时调用---

# 卡片组件

卡片组件将信息集成在卡片容器带有可选页眉和页脚
## 何时使用

- 仪表板卡- 内容区域
- 产品展示
- 信息分组

## 基本用法

```vue
<template>
  <el-card>
    <template #header>
      <div class="card-header">
        <span>Card name</span>
        <el-button class="button" text>Operation button</el-button>
      </div>
    </template>
    Card content
  </el-card>
</template>
```

## 简单卡
```vue
<template>
  <el-card>
    Card content without header
  </el-card>
</template>
```

## 带图
```vue
<template>
  <el-card :body-style="{ padding: '0px' }">
    <img src="https://example.com/image.jpg" class="image" />
    <div style="padding: 14px">
      <span>Yummy hamburger</span>
      <div class="bottom">
        <time class="time">{{ currentDate }}</time>
        <el-button text class="button">Operating</el-button>
      </div>
    </div>
  </el-card>
</template>
```

## 阴影

```vue
<template>
  <el-card shadow="always">Always show shadow</el-card>
  <el-card shadow="hover">Show shadow on hover</el-card>
  <el-card shadow="never">Never show shadow</el-card>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| header | 卡片标题 | `string` | |
| footer | 卡片页脚 | `string` | |
| body-style | 卡片主体CSS 样式 | `CSSProperties` | |
| header-class | 卡片页眉自定义类名 | `string` | |
| body-class | 卡片主体自定义类名 | `string` | |
| footer-class | 卡片页脚自定义类名 | `string` | |
| shadow | 何时显示卡片阴影 | `'always' \| 'never' \| 'hover'` | `'always'` |

### 插槽

| Name | Description |
|------|-------------|
| default | 自定义默认内容|
| header | 卡片页眉内容|
| footer | 卡片页脚内容|

## 常见模式

### 仪表板卡
```vue
<template>
  <el-card class="dashboard-card">
    <template #header>
      <div class="card-header">
        <span>{{ title }}</span>
        <el-button link type="primary">View All</el-button>
      </div>
    </template>
    <div class="card-content">
      <el-statistic :value="value" />
    </div>
  </el-card>
</template>

<script setup>
defineProps({
  title: String,
  value: Number
})
</script>
```

## 最佳实践
1. 对交互式卡片使用 `shadow="hover"`
2. 设置 `body-style` 以自定义内边3. 使用页眉和页脚插槽来组织结构化内容