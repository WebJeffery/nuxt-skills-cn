---
name: "el-statistic"
description: "用于显示数值统计统计数值组件。当用户需要显示计数、金额、排名倒计时时调用---

# Statistic 组件

Statistic 显示数支持可选格式化和倒计时
## 何时使用

- 仪表板统- 计数显示
- 金额显示
- 倒计时器

## 基本用法

```vue
<template>
  <el-statistic title="Total Users" :value="268500" />
</template>
```

## 带前缀/后缀

```vue
<template>
  <el-statistic title="Revenue" :value="100000" prefix="$" suffix="USD" />
</template>
```

## 格式
```vue
<template>
  <el-statistic
    title="Formatted Number"
    :value="1234567.89"
    :precision="2"
    group-separator=","
    decimal-separator="."
  />
</template>
```

## 自定义格式化

```vue
<template>
  <el-statistic
    title="Custom Format"
    :value="1234567"
    :formatter="formatter"
  />
</template>

<script setup>
const formatter = (value) => {
  return value.toLocaleString('en-US')
}
</script>
```

## 倒计
```vue
<template>
  <el-countdown
    title="Countdown"
    :value="deadline"
    @finish="handleFinish"
  />
</template>

<script setup>
import { ref } from 'vue'

const deadline = ref(Date.now() + 1000 * 60 * 60 * 24)

const handleFinish = () => {
  console.log('Countdown finished')
}
</script>
```

## 倒计时格
```vue
<template>
  <el-countdown
    title="Custom Format"
    :value="deadline"
    format="DD days HH:mm:ss"
  />
</template>
```

## API 参考
### Statistic 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| value | 数| `number` | `0` |
| title | 标题 | `string` | |
| prefix | 前缀 | `string` | |
| suffix | 后缀 | `string` | |
| precision | 小数精度 | `number` | `0` |
| decimal-separator | 小数分隔| `string` | `'.'` |
| group-separator | 千位分隔| `string` | `','` |
| formatter | 自定义格式化 | `(value: number) => string \| number` | |
| value-style | 值样式| `CSSProperties` | |

### Statistic 插槽

| Name | Description |
|------|-------------|
| title | 自定义标签|
| prefix | 自定义前缀 |
| suffix | 自定义后缀 |

### Countdown 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| value | 目标时间 | `number \| Dayjs` | |
| title | 标题 | `string` | |
| prefix | 前缀 | `string` | |
| suffix | 后缀 | `string` | |
| format | 显示格式 | `string` | `'HH:mm:ss'` |
| value-style | 值样式| `CSSProperties` | |

### Countdown 事件

| Name | Description | Type |
|------|-------------|------|
| change | 时间差变| `(value: number) => void` |
| finish | 倒计时结| `() => void` |

### Countdown 插槽

| Name | Description |
|------|-------------|
| title | 自定义标签|
| prefix | 自定义前缀 |
| suffix | 自定义后缀 |

## 最佳实践
1. 使用 `precision` 用于小数2. 使用 `group-separator` 用于大数3. 使用 Countdown 用于时间敏感显