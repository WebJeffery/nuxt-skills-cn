---
name: "el-radio"
description: "用于在多个选项中进行单选单选框组件。当用户需要允许用户从列表中选择一个选项、创建单选组实现互斥选择时调用---

# Radio 组件

单选框组件提供在多个选项中进行单选功能,具有各种样式
## 何时使用

- 从选项中进行单- 互斥选择
- 表单单选字- 设置选择

## 基本用法

```vue
<template>
  <el-radio v-model="radio" value="1">Option A</el-radio>
  <el-radio v-model="radio" value="2">Option B</el-radio>
</template>

<script setup>
import { ref } from 'vue'

const radio = ref('1')
</script>
```

## 禁用

```vue
<template>
  <el-radio v-model="radio" value="1" disabled>Disabled</el-radio>
</template>
```

## 单选组

```vue
<template>
  <el-radio-group v-model="radio">
    <el-radio value="1">Option A</el-radio>
    <el-radio value="2">Option B</el-radio>
    <el-radio value="3">Option C</el-radio>
  </el-radio-group>
</template>
```

## 带边
```vue
<template>
  <el-radio-group v-model="radio">
    <el-radio value="1" border>Option A</el-radio>
    <el-radio value="2" border>Option B</el-radio>
  </el-radio-group>
</template>
```

## 单选按钮
```vue
<template>
  <el-radio-group v-model="radio">
    <el-radio-button value="1">Option A</el-radio-button>
    <el-radio-button value="2">Option B</el-radio-button>
    <el-radio-button value="3">Option C</el-radio-button>
  </el-radio-group>
</template>
```

## Options 属性
```vue
<template>
  <el-radio-group v-model="radio" :options="options" />
</template>

<script setup>
import { ref } from 'vue'

const radio = ref('1')
const options = [
  { value: '1', label: 'Option A' },
  { value: '2', label: 'Option B' },
  { value: '3', label: 'Option C' }
]
</script>
```

## API 参考
### Radio 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string \| number \| boolean` | |
| value | 单选框| `string \| number \| boolean` | |
| label | 标签文本 | `string \| number \| boolean` | |
| disabled | 是否禁用 | `boolean` | `false` |
| border | 是否添加边框 | `boolean` | `false` |
| size | 尺寸 | `'large' \| 'default' \| 'small'` | |
| name | 原生 name 属性| `string` | |

### Radio 事件

| Name | Description | Type |
|------|-------------|------|
| change | 当值改变时触发 | `(value: string \| number \| boolean) => void` |

### RadioGroup 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string \| number \| boolean` | |
| size | 单选框尺寸 | `'large' \| 'default' \| 'small'` | `'default'` |
| disabled | 是否禁用 | `boolean` | `false` |
| text-color | 激活时字体颜色| `string` | `'#ffffff'` |
| fill | 激活时背景颜色| `string` | `'#409eff'` |
| options | 选项数据 | `Array<{[key: string]: any}>` | |
| props | 配置选项 | `object` | |
| type | 组件类型 | `'radio' \| 'button'` | `'radio'` |

## 最佳实践
1. 2.6.0 起使`value` 属性而非 `label`)作为单选框2. 使用 `el-radio-button` 实现按钮样式组
3. 使用 `options` 实现动态单选列