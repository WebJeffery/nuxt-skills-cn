---
name: "el-progress"
description: "用于显示操作进度和状态进度条组件。当用户需要显示上传进度、任务完成度加载状态时调用---

# 进度条组件
进度条组件以线性、圆形仪表盘样式显示当前操作进度
## 何时使用

- 上传进度
- 任务完成- 加载状态- 状态指示器

## 基本用法

```vue
<template>
  <el-progress :percentage="50" />
</template>
```

## 内部百分
```vue
<template>
  <el-progress :percentage="70" :stroke-width="20" text-inside />
</template>
```

## 自定义颜色
```vue
<template>
  <el-progress :percentage="percentage" :color="customColor" />
</template>

<script setup>
import { ref } from 'vue'

const percentage = ref(70)
const customColor = '#409eff'
</script>
```

## 环形进度
```vue
<template>
  <el-progress type="circle" :percentage="75" />
</template>
```

## 仪表盘进度条

```vue
<template>
  <el-progress type="dashboard" :percentage="percentage">
    <template #default="{ percentage }">
      <span class="percentage-value">{{ percentage }}%</span>
    </template>
  </el-progress>
</template>
```

## 不确定进度条

```vue
<template>
  <el-progress :percentage="50" indeterminate :duration="3" />
</template>
```

## 条纹进度
```vue
<template>
  <el-progress :percentage="70" striped striped-flow />
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| percentage | 百分必需) | `number (0-100)` | `0` |
| type | 进度条类型| `'line' \| 'circle' \| 'dashboard'` | `'line'` |
| stroke-width | 进度条宽| `number` | `6` |
| text-inside | 百分比放在内容| `boolean` | `false` |
| status | 当前状态| `'success' \| 'exception' \| 'warning'` | |
| indeterminate | 设置不确定进| `boolean` | `false` |
| duration | 动画持续时间 | `number` | `3` |
| color | 背景颜色 | `string \| function \| array` | `''` |
| width | 画布宽度(圆形/仪表 | `number` | `126` |
| show-text | 显示百分| `boolean` | `true` |
| stroke-linecap | 路径末端形| `'butt' \| 'round' \| 'square'` | `'round'` |
| format | 自定义文本格| `(percentage: number) => string` | |
| striped | 进度条上条| `boolean` | `false` |
| striped-flow | 动画条纹 | `boolean` | `false` |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| default | 自定义内容| `{ percentage: number }` |

## 最佳实践
1. 使用 `type="circle"` 进行紧凑显示
2. 对未知进度使`indeterminate`
3. 使用 `format` 进行自定义文本显