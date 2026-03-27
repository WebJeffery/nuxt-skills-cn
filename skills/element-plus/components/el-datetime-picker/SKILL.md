---
name: "el-datetime-picker"
description: "用于同时选择日期和时间日期时间选择器组件。当用户需要在一个选择器中选择日期和时间时调用---

# DateTimePicker 组件

DateTimePicker 允许在一个选择器中选择日期和时间
## 何时使用

- 日期时间选择
- 日期时间范围选择
- 日程安排
- 事件创建

## 基本用法

```vue
<template>
  <el-date-picker
    v-model="value"
    type="datetime"
    placeholder="Select date and time"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
</script>
```

## 日期时间范围

```vue
<template>
  <el-date-picker
    v-model="value"
    type="datetimerange"
    range-separator="To"
    start-placeholder="Start date"
    end-placeholder="End date"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref([])
</script>
```

## 带默认时
```vue
<template>
  <el-date-picker
    v-model="value"
    type="datetimerange"
    :default-time="defaultTime"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref([])
const defaultTime = [
  new Date(2000, 1, 1, 0, 0, 0),
  new Date(2000, 1, 1, 23, 59, 59)
]
</script>
```

## 日期格式

```vue
<template>
  <el-date-picker
    v-model="value"
    type="datetime"
    format="YYYY/MM/DD HH:mm:ss"
    value-format="YYYY-MM-DD HH:mm:ss"
  />
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string \| Date \| number \| array` | |
| type | 选择器类型| `'datetime' \| 'datetimerange'` | `'datetime'` |
| format | 显示格式 | `string` | `'YYYY-MM-DD HH:mm:ss'` |
| value-format | 值格| `string` | |
| default-time | 默认时间| `Date \| [Date, Date]` | |
| date-format | 面板日期格式 | `string` | `'YYYY-MM-DD'` |
| time-format | 面板时间格式 | `string` | `'HH:mm:ss'` |
| arrow-control | 使用箭头按钮 | `boolean` | `false` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值改| `(value) => void` |
| blur | 输入失焦 | `(e: FocusEvent) => void` |
| focus | 输入聚焦 | `(e: FocusEvent) => void` |
| calendar-change | 日历选择改变 | `(val) => void` |
| visible-change | 下拉可见性改| `(visibility: boolean) => void` |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| focus | 聚焦输入 | `() => void` |
| blur | 失焦输入 | `() => void` |

## 最佳实践
1. 使用 `default-time` 用于一致时间2. 使用 `value-format` 用于字符串3. 使用 `arrow-control` 用于精确时间选择
