---
name: "el-calendar"
description: "用于显示日期日历组件。当用户需要显示日历视图、选择日期显示计划事件时调用---

# Calendar 组件

Calendar 组件显示日期,具有可自定义内容和范围
## 何时使用

- 日期选择
- 事件日历
- 计划显示
- 日期范围视图

## 基本用法

```vue
<template>
  <el-calendar v-model="date" />
</template>

<script setup>
import { ref } from 'vue'

const date = ref(new Date())
</script>
```

## 自定义内容
```vue
<template>
  <el-calendar v-model="date">
    <template #dateCell="{ data }">
      <p>{{ data.day.split('-')[2] }}</p>
    </template>
  </el-calendar>
</template>
```

## 范围

```vue
<template>
  <el-calendar :range="[startDate, endDate]" />
</template>

<script setup>
const startDate = new Date(2024, 0, 1)
const endDate = new Date(2024, 1, 28)
</script>
```

## 自定义页
```vue
<template>
  <el-calendar v-model="date">
    <template #header="{ date }">
      <span>{{ date }}</span>
    </template>
  </el-calendar>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `Date` | |
| range | 时间范围 | `[Date, Date]` | |
| controller-type | 控制器类型| `'button' \| 'select'` | `'button'` |
| formatter | 格式化标签| `(value, type) => string \| number` | |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| date-cell | 自定义日期单元格 | `{ data: { type, isSelected, day, date } }` |
| header | 自定义页| `{ date: string }` |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| selectedDay | 当前选中日| `ComputedRef<Dayjs>` |
| pickDay | 选择特定日期 | `(day) => void` |
| selectDate | 选择日期 | `(type) => void` |

## 最佳实践
1. 使用 `date-cell` 插槽进行自定义内容2. 使用 `range` 用于有限日期视3. 自定义页眉用于品牌化
