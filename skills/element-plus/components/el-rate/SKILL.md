---
name: "el-rate"
description: "用于评分功能评分组件。当用户需要收集用户评分、显示星级评分允许反馈评分时调用---

# Rate 组件

Rate 组件用于评分,具有可自定义图标和级别
## 何时使用

- 用户评分
- 产品评论
- 反馈评分
- 星级评分

## 基本用法

```vue
<template>
  <el-rate v-model="value" />
</template>

<script setup>
import { ref } from 'vue'

const value = ref(0)
</script>
```

## 带半径
```vue
<template>
  <el-rate v-model="value" allow-half />
</template>
```

## 带文
```vue
<template>
  <el-rate
    v-model="value"
    show-text
    :texts="['Bad', 'Poor', 'Fair', 'Good', 'Excellent']"
  />
</template>
```

## 可清
```vue
<template>
  <el-rate v-model="value" clearable />
</template>
```

## 自定义图
```vue
<template>
  <el-rate
    v-model="value"
    :icons="[StarFilled, StarFilled, StarFilled]"
    :void-icon="Star"
  />
</template>

<script setup>
import { StarFilled, Star } from '@element-plus/icons-vue'
</script>
```

## 只读

```vue
<template>
  <el-rate
    v-model="value"
    disabled
    show-score
    score-template="{value} points"
  />
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `number` | `0` |
| max | 最大评| `number` | `5` |
| size | 尺寸 | `'large' \| 'default' \| 'small'` | |
| disabled | 只读 | `boolean` | `false` |
| allow-half | 允许半星 | `boolean` | `false` |
| low-threshold | 中阈| `number` | `2` |
| high-threshold | 高阈| `number` | `4` |
| colors | 级别颜色| `string[] \| Record<number, string>` | |
| void-color | 未选中颜色| `string` | `'#c6d1de'` |
| icons | 图标组件 | `string[] \| Component[]` | |
| void-icon | 未选中图| `string \| Component` | |
| show-text | 显示文本 | `boolean` | `false` |
| show-score | 显示分数 | `boolean` | `false` |
| text-color | 文本颜色 | `string` | `''` |
| texts | 文本数组 | `string[]` | |
| score-template | 分数模板 | `string` | `'{value}'` |
| clearable | 可以重置0 | `boolean` | `false` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值改变时触发 | `(value: number) => void` |

## 最佳实践
1. 使用 `allow-half` 进行精确评分
2. 使用 `show-score` 进行只读显示
3. 使用 `clearable` 允许重置
