---
name: "el-date-picker"
description: "用于日期输入日期选择器组件。当用户需要选择日期、日期范围、月份、年份日期时间值时调用---

# DatePicker 组件

DatePicker 允许用户选择日期、日期范围、月份、年份日期时间值
## 何时使用

- 日期选择
- 日期范围选择
- 月份/年份选择
- 日期时间输入

## 基本用法

```vue
<template>
  <el-date-picker
    v-model="value"
    type="date"
    placeholder="Pick a day"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
</script>
```

## 日期范围

```vue
<template>
  <el-date-picker
    v-model="value"
    type="daterange"
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

## 带快捷选项

```vue
<template>
  <el-date-picker
    v-model="value"
    type="date"
    placeholder="Pick a day"
    :shortcuts="shortcuts"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
const shortcuts = [
  {
    text: 'Today',
    value: new Date()
  },
  {
    text: 'Yesterday',
    value: () => {
      const date = new Date()
      date.setTime(date.getTime() - 3600 * 1000 * 24)
      return date
    }
  },
  {
    text: 'A week ago',
    value: () => {
      const date = new Date()
      date.setTime(date.getTime() - 3600 * 1000 * 24 * 7)
      return date
    }
  }
]
</script>
```

## 日期格式

```vue
<template>
  <el-date-picker
    v-model="value"
    type="date"
    placeholder="Pick a day"
    format="YYYY/MM/DD"
    value-format="YYYY-MM-DD"
  />
</template>
```

## 禁用日期

```vue
<template>
  <el-date-picker
    v-model="value"
    type="date"
    placeholder="Pick a day"
    :disabled-date="disabledDate"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')

const disabledDate = (time) => {
  return time.getTime() > Date.now()
}
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string \| Date \| number \| array` | |
| type | 选择器类型| `'year' \| 'month' \| 'date' \| 'dates' \| 'datetime' \| 'week' \| 'datetimerange' \| 'daterange' \| 'monthrange'` | `'date'` |
| format | 显示格式 | `string` | `'YYYY-MM-DD'` |
| value-format | 值格| `string` | |
| readonly | 只读 | `boolean` | `false` |
| disabled | 禁用 | `boolean` | `false` |
| editable | 输入可编| `boolean` | `true` |
| clearable | 显示清除按钮 | `boolean` | `true` |
| placeholder | 占位| `string` | |
| start-placeholder | 开始占位符 | `string` | |
| end-placeholder | 结束占位| `string` | |
| range-separator | 范围分隔| `string` | `'-'` |
| default-value | 默认日历日期 | `Date \| [Date, Date]` | |
| disabled-date | 禁用日期函数 | `(date: Date) => boolean` | |
| shortcuts | 快捷选项 | `Array<{ text: string, value: Date \| Function }>` | |
| unlink-panels | 取消范围面板联动 | `boolean` | `false` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值改| `(value) => void` |
| blur | 输入失焦 | `(e: FocusEvent) => void` |
| focus | 输入聚焦 | `(e: FocusEvent) => void` |
| calendar-change | 日历选择改变 | `(val) => void` |
| visible-change | 下拉框可见性改| `(visibility: boolean) => void` |

### 插槽

| Name | Description |
|------|-------------|
| default | 自定义单元格内容 |
| range-separator | 自定义范围分隔符 |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| focus | 聚焦输入 | `() => void` |
| blur | 失焦输入 | `() => void` |
| handleOpen | 打开选择| `() => void` |
| handleClose | 关闭选择| `() => void` |

## 最佳实践
1. 使用 `value-format` 获取字符串2. 使用 `shortcuts` 进行快速选择
3. 使用 `disabled-date` 限制选择
