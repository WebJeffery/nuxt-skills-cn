---
name: "el-time-select"
description: "用于从固定时间选项中选择时间选择组件。当用户需要从预定义时间段或计划时间中选择时调用。"
---

# TimeSelect 组件

TimeSelect 提供从固定时间选项中选择功能。

## 何时使用

- 固定时间选择
- 计划时段
- 预约时间
- 营业时间

## 基本用法

```vue
<template>
  <el-time-select
    v-model="value"
    start="08:30"
    step="00:15"
    end="18:30"
    placeholder="选择时间"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
</script>
```

## 时间范围

```vue
<template>
  <el-time-select
    v-model="startTime"
    placeholder="开始时间"
    start="08:30"
    step="00:30"
    end="18:30"
    :max-time="endTime"
  />
  <el-time-select
    v-model="endTime"
    placeholder="结束时间"
    start="08:30"
    step="00:30"
    end="18:30"
    :min-time="startTime"
  />
</template>

<script setup>
import { ref } from 'vue'

const startTime = ref('')
const endTime = ref('')
</script>
```

## 时间格式

```vue
<template>
  <el-time-select
    v-model="value"
    format="HH:mm A"
    start="08:00"
    step="01:00"
    end="18:00"
  />
</template>
```

## API 参考

### 属性

| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定值 | `string` | - |
| disabled | 禁用 | `boolean` | `false` |
| editable | 输入可编辑 | `boolean` | `true` |
| clearable | 显示清除按钮 | `boolean` | `true` |
| size | 尺寸 | `'large' \| 'default' \| 'small'` | `'default'` |
| placeholder | 占位符 | `string` | - |
| start | 开始时间 | `string` | `'09:00'` |
| end | 结束时间 | `string` | `'18:00'` |
| step | 时间步长 | `string` | `'00:30'` |
| min-time | 最小时间 | `string` | - |
| max-time | 最大时间 | `string` | - |
| format | 时间格式 | `string` | `'HH:mm'` |
| include-end-time | 包含结束时间 | `boolean` | `false` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值变化 | `(value: string) => void` |
| blur | 输入失焦 | `(e: FocusEvent) => void` |
| focus | 输入聚焦 | `(e: FocusEvent) => void` |
| clear | 清除按钮点击 | `() => void` |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| focus | 聚焦输入 | `() => void` |
| blur | 失焦输入 | `() => void` |

## 最佳实践

1. 使用 `min-time`/`max-time` 进行范围验证
2. 根据用例设置适当 `step`
3. 使用 `format` 进行自定义显示
