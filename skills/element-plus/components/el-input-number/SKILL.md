---
name: "el-input-number"
description: "用于数字输入的输入数字组件，带有控制按钮。当用户需要通过增减按钮输入数字、设置数字范围或强制数字输入时调用。"
---

# InputNumber 组件

InputNumber 允许用户通过控件和验证输入数值。

## 何时使用

- 数量输入
- 数值设置
- 范围限制的数字
- 精确的数字输入

## 基本用法

```vue
<template>
  <el-input-number v-model="num" :min="1" :max="10" />
</template>

<script setup>
import { ref } from 'vue'

const num = ref(1)
</script>
```

## 禁用

```vue
<template>
  <el-input-number v-model="num" disabled />
</template>
```

## 步长

```vue
<template>
  <el-input-number v-model="num" :step="2" />
</template>
```

## 严格步长

```vue
<template>
  <el-input-number v-model="num" :step="2" step-strictly />
</template>
```

## 精度

```vue
<template>
  <el-input-number v-model="num" :precision="2" :step="0.1" :max="10" />
</template>
```

## 尺寸

```vue
<template>
  <el-input-number v-model="num" size="large" />
  <el-input-number v-model="num" />
  <el-input-number v-model="num" size="small" />
</template>
```

## 控件位置

```vue
<template>
  <el-input-number v-model="num" controls-position="right" />
</template>
```

## API 参考

### 属性

| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定值 | `number` | `0` |
| min | 最小值 | `number` | `-Infinity` |
| max | 最大值 | `number` | `Infinity` |
| step | 增量步长 | `number` | `1` |
| step-strictly | 要求步长的倍数 | `boolean` | `false` |
| precision | 小数精度 | `number` | - |
| size | 尺寸 | `'large' \| 'default' \| 'small'` | `'default'` |
| disabled | 是否禁用 | `boolean` | `false` |
| controls | 显示控制按钮 | `boolean` | `true` |
| controls-position | 按钮位置 | `'' \| 'right'` | - |
| name | 原生 name 属性 | `string` | - |
| label | Aria 标签 | `string` | - |
| placeholder | 占位符 | `string` | - |
| value-on-clear | 清除时的值 | `'min' \| 'max' \| number \| null` | `null` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值变化时触发 | `(value: number, oldValue: number) => void` |
| input | 输入时触发 | `(value: number \| null \| undefined) => void` |
| blur | 失焦时触发 | `(event: FocusEvent) => void` |
| focus | 聚焦时触发 | `(event: FocusEvent) => void` |

### 插槽

| Name | Description |
|------|-------------|
| decrease | 自定义减少按钮 |
| increase | 自定义增加按钮 |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| focus | 聚焦输入 | `() => void` |
| blur | 失焦输入 | `() => void` |

## 最佳实践

1. 设置 `min` 和 `max` 用于有效范围
2. 使用 `precision` 用于小数
3. 使用 `controls-position="right"` 用于紧凑布局
