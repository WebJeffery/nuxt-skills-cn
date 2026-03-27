---
name: "el-date-picker-panel"
description: "DatePickerPanel DatePicker 核心面板组件用于自定义日期选择 UI。当用户需要构建自定义日期选择器界面而不带下拉包装器时调用---

# DatePickerPanel 组件

DatePickerPanel DatePicker 核心组件用于构建自定义日期选择界面
## 何时使用

- 自定义日期选择UI
- 嵌入式日期选择
- 日历小部- 自定义下拉实现
## 基本用法

```vue
<template>
  <el-date-picker-panel v-model="date" />
</template>

<script setup>
import { ref } from 'vue'

const date = ref('')
</script>
```

## 类型

```vue
<template>
  <el-date-picker-panel v-model="date" type="date" />
  <el-date-picker-panel v-model="month" type="month" />
  <el-date-picker-panel v-model="year" type="year" />
  <el-date-picker-panel v-model="datetime" type="datetime" />
  <el-date-picker-panel v-model="daterange" type="daterange" />
</template>
```

## 无边
```vue
<template>
  <el-date-picker-panel v-model="date" :border="false" />
</template>
```

## 禁用

```vue
<template>
  <el-date-picker-panel v-model="date" disabled />
</template>
```

## 带快捷方
```vue
<template>
  <el-date-picker-panel
    v-model="date"
    :shortcuts="shortcuts"
  />
</template>

<script setup>
import { ref } from 'vue'

const date = ref('')
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
  }
]
</script>
```

## 禁用日期

```vue
<template>
  <el-date-picker-panel
    v-model="date"
    :disabled-date="disabledDate"
  />
</template>

<script setup>
import { ref } from 'vue'

const date = ref('')

const disabledDate = (time) => {
  return time.getTime() > Date.now()
}
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `number \| string \| Date \| array` | `''` |
| border | 显示边框 | `boolean` | `true` |
| disabled | 禁用 | `boolean` | `false` |
| clearable | 显示清除按钮 | `boolean` | `true` |
| editable | 输入可编| `boolean` | `true` |
| type | 选择器类型| `'year' \| 'years' \| 'month' \| 'months' \| 'date' \| 'dates' \| 'datetime' \| 'week' \| 'datetimerange' \| 'daterange' \| 'monthrange' \| 'yearrange'` | `'date'` |
| default-value | 默认日历日期 | `Date \| [Date, Date]` | |
| default-time | 范围默认时| `Date \| [Date, Date]` | |
| value-format | 值格| `string` | |
| date-format | 日期显示格式 | `string` | `'YYYY-MM-DD'` |
| time-format | 时间显示格式 | `string` | `'HH:mm:ss'` |
| unlink-panels | 取消链接范围面板 | `boolean` | `false` |
| disabled-date | 禁用日期函数 | `(date) => boolean` | |
| shortcuts | 快捷方式选项 | `Array<{ text, value }>` | `[]` |
| cell-class-name | 自定义单元格| `(date) => string` | |
| show-footer | 显示页脚 | `boolean` | `false` |
| show-confirm | 显示确认按钮 | `boolean` | `false` |
| show-week-number | 显示周数 | `boolean` | `false` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| calendar-change | 日历选择改变 | `(val) => void` |
| panel-change | 导航按钮点击 | `(date, mode, view) => void` |
| clear | 清除按钮点击 | `() => void` |

### 插槽

| Name | Description |
|------|-------------|
| default | 自定义单元格内容 |
| prev-month | 上个月图|
| next-month | 下个月图|
| prev-year | 上一年图|
| next-year | 下一年图|

## 最佳实践
1. 用于自定义日期选择器实现2. Popover 结合用于下拉行为
3. 使用 `shortcuts` 用于快速选择
