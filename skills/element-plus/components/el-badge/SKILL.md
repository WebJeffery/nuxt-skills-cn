---
name: "el-badge"
description: "用于在按钮和图标上显示数字状态标记徽标组件。当用户需要显示通知计数、状态指示器注意标记时调用
---

# 徽标组件

徽标组件在按钮和图标上显示数字状态标签用于通知和状态指示器
## 何时使用

- 通知计数
- 状态指示器
- 未读消息徽标
- 注意标记

## 基本用法

```vue
<template>
  <el-badge :value="12" class="item">
    <el-button>comments</el-button>
  </el-badge>
  <el-badge :value="3" class="item">
    <el-button>replies</el-button>
  </el-badge>
</template>
```

## 最大
```vue
<template>
  <el-badge :value="200" :max="99" class="item">
    <el-button>comments</el-button>
  </el-badge>
</template>
```

## 自定义内容
```vue
<template>
  <el-badge value="new" class="item">
    <el-button>comments</el-button>
  </el-badge>
  <el-badge value="hot" class="item">
    <el-button>replies</el-button>
  </el-badge>
</template>
```

## 红点

```vue
<template>
  <el-badge is-dot class="item">
    <el-button>query</el-button>
  </el-badge>
  <el-badge is-dot class="item">
    <el-icon><Bell /></el-icon>
  </el-badge>
</template>
```

## 偏移

```vue
<template>
  <el-badge :value="5" :offset="[10, 10]">
    <el-button>Offset badge</el-button>
  </el-badge>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| value | 显示| `string \| number` | `''` |
| max | 最大超过时显`{max}+` | `number` | `99` |
| is-dot | 显示为圆| `boolean` | `false` |
| hidden | 隐藏徽标 | `boolean` | `false` |
| type | 徽标类型 | `'primary' \| 'success' \| 'warning' \| 'danger' \| 'info'` | `'danger'` |
| show-zero | 值为零时显示徽标 | `boolean` | `true` |
| color | 圆点背景颜色| `string` | |
| offset | 徽标签偏移量 | `[number, number]` | `[0, 0]` |
| badge-style | 徽标自定义样式 | `CSSProperties` | |
| badge-class | 徽标自定义类名 | `string` | |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| default | 自定义默认内容| |
| content | 自定义徽标内容| `{ value: string }` |

## 常见模式

### 通知徽标

```vue
<template>
  <el-badge :value="unreadCount" :hidden="unreadCount === 0">
    <el-icon><Bell /></el-icon>
  </el-badge>
</template>

<script setup>
import { ref } from 'vue'

const unreadCount = ref(5)
</script>
```

## 最佳实践
1. 使用 `max` 防止大数字溢2. 使用 `is-dot` 进行简单状态指3. 当计数为零且 `show-zero` false 时设`hidden`
