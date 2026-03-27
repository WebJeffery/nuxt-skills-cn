---
name: "el-timeline"
description: "用于可视化显示时间线活动时间线组件。当用户需要显示按时间顺序排列事件、活动历史过程日志时调用---

# Timeline 组件

Timeline 组件可视化显示带有时间戳时间线活动
## 何时使用

- 活动历史
- 按时间顺序事件
- 过程日志
- 版本历史

## 基本用法

```vue
<template>
  <el-timeline>
    <el-timeline-item
      v-for="(activity, index) in activities"
      :key="index"
      :timestamp="activity.timestamp"
    >
      {{ activity.content }}
    </el-timeline-item>
  </el-timeline>
</template>

<script setup>
const activities = [
  { content: 'Event 1', timestamp: '2024-01-01' },
  { content: 'Event 2', timestamp: '2024-01-02' },
  { content: 'Event 3', timestamp: '2024-01-03' }
]
</script>
```

## 模式

```vue
<template>
  <el-timeline mode="alternate">
    <el-timeline-item timestamp="2024-01-01">Event 1</el-timeline-item>
    <el-timeline-item timestamp="2024-01-02">Event 2</el-timeline-item>
  </el-timeline>
</template>
```

## 自定义节
```vue
<template>
  <el-timeline>
    <el-timeline-item
      timestamp="2024-01-01"
      color="#0bbd87"
      size="large"
    >
      Custom node
    </el-timeline-item>
    <el-timeline-item
      timestamp="2024-01-02"
      :icon="Check"
    >
      With icon
    </el-timeline-item>
  </el-timeline>
</template>

<script setup>
import { Check } from '@element-plus/icons-vue'
</script>
```

## 自定义时间戳

```vue
<template>
  <el-timeline>
    <el-timeline-item timestamp="2024-01-01" placement="top">
      Timestamp on top
    </el-timeline-item>
  </el-timeline>
</template>
```

## 反向

```vue
<template>
  <el-timeline reverse>
    <el-timeline-item timestamp="2024-01-01">First</el-timeline-item>
    <el-timeline-item timestamp="2024-01-02">Second</el-timeline-item>
  </el-timeline>
</template>
```

## API 参考
### Timeline 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| reverse | 反向顺序 | `boolean` | `false` |
| mode | 时间线位置 | `'start' \| 'alternate' \| 'alternate-reverse' \| 'end'` | `'start'` |

### TimelineItem 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| timestamp | 时间戳内容| `string` | `''` |
| hide-timestamp | 隐藏时间| `boolean` | `false` |
| center | 垂直居中 | `boolean` | `false` |
| placement | 时间戳位置 | `'top' \| 'bottom'` | `'bottom'` |
| type | 节点类型 | `'primary' \| 'success' \| 'warning' \| 'danger' \| 'info'` | `''` |
| color | 节点背景颜色| `string` | `''` |
| size | 节点大小 | `'normal' \| 'large'` | `'normal'` |
| icon | 图标组件 | `string \| Component` | |
| hollow | 图标是空心 | `boolean` | `false` |

### TimelineItem 插槽

| Name | Description |
|------|-------------|
| default | 自定义内容|
| dot | 自定义节|

## 最佳实践
1. 使用 `mode="alternate"` 用于交替布局
2. 使用 `reverse` 用于最新优先排序
3. 使用图标自定义节点以进行视觉区分
