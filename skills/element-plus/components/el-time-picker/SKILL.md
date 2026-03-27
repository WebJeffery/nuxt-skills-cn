---
name: "el-time-picker"
description: "用于时间输入时间选择器组件。当用户需要选择时间值、时间范围或限制时间选择时调用。"
---

# TimePicker 组件

TimePicker 允许用户选择时间值，支持可选范围选择。

## 何时使用

- 时间选择
- 时间范围选择
- 限制时间选项
- 日程安排

## 基本用法

```vue
<template>
  <el-time-picker v-model="value" placeholder="任意时间" />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
</script>
```

## 时间范围

```vue
<template>
  <el-time-picker
    v-model="value"
    is-range
    range-separator="至"
    start-placeholder="开始时间"
    end-placeholder="结束时间"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref([])
</script>
```

## 箭头控制

```vue
<template>
  <el-time-picker
    v-model="value"
    arrow-control
    placeholder="任意时间"
  />
</template>
```

## 限制时间范围

```vue
<template>
  <el-time-picker
    v-model="value"
    :disabled-hours="disabledHours"
    :disabled-minutes="disabledMinutes"
    :disabled-seconds="disabledSeconds"
    placeholder="任意时间"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')

const disabledHours = () => [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23].filter(x => x < 9 || x > 18)

const disabledMinutes = (hour) => [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59].filter(x => x > 30)

const disabledSeconds = () => []
</script>
```

## API 参考

### 属性

| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定值 | `string \| Date \| number \| array` | - |
| readonly | 只读 | `boolean` | `false` |
| disabled | 禁用 | `boolean` | `false` |
| editable | 输入可编辑 | `boolean` | `true` |
| clearable | 显示清除按钮 | `boolean` | `true` |
| size | 尺寸 | `'large' \| 'default' \| 'small'` | - |
| placeholder | 占位符 | `string` | - |
| start-placeholder | 开始占位符 | `string` | - |
| end-placeholder | 结束占位符 | `string` | - |
| is-range | 启用范围模式 | `boolean` | `false` |
| arrow-control | 使用箭头按钮 | `boolean` | `false` |
| format | 显示格式 | `string` | `'HH:mm:ss'` |
| value-format | 值格式 | `string` | - |
| disabled-hours | 禁用小时 | `(role, comparingDate?) => number[]` | - |
| disabled-minutes | 禁用分钟 | `(hour, role, comparingDate?) => number[]` | - |
| disabled-seconds | 禁用秒 | `(hour, minute, role, comparingDate?) => number[]` | - |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值变化 | `(value) => void` |
| blur | 输入失焦 | `(e: FocusEvent) => void` |
| focus | 输入聚焦 | `(e: FocusEvent) => void` |
| visible-change | 下拉可见性变化 | `(visibility: boolean) => void` |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| focus | 聚焦输入 | `() => void` |
| blur | 失焦输入 | `() => void` |
| handleOpen | 打开选择器 | `() => void` |
| handleClose | 关闭选择器 | `() => void` |

## 最佳实践

1. 使用 `arrow-control` 进行精确选择
2. 使用 `disabled-hours/minutes/seconds` 限制选项
3. 使用 `is-range` 进行时间范围选择
