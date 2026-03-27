---
name: "el-checkbox"
description: "用于多选选择复选框组件。当用户需要允许用户从列表中选择多个选项、创建全选功能在状态之间切换时调用---

# Checkbox 组件

复选框组件提供一组用于多选选项,具有各种样式和配置
## 何时使用

- 从选项中进行多- 在状态之间切- 全选功- 表单多选字
## 基本用法

```vue
<template>
  <el-checkbox v-model="checked">Option</el-checkbox>
</template>

<script setup>
import { ref } from 'vue'

const checked = ref(false)
</script>
```

## 禁用状态
```vue
<template>
  <el-checkbox v-model="checked" disabled>Disabled</el-checkbox>
</template>
```

## 复选框
```vue
<template>
  <el-checkbox-group v-model="checkList">
    <el-checkbox value="Option A">Option A</el-checkbox>
    <el-checkbox value="Option B">Option B</el-checkbox>
    <el-checkbox value="Option C">Option C</el-checkbox>
  </el-checkbox-group>
</template>

<script setup>
import { ref } from 'vue'

const checkList = ref(['Option A', 'Option B'])
</script>
```

## Options 属性
```vue
<template>
  <el-checkbox-group v-model="checkList" :options="options" />
</template>

<script setup>
import { ref } from 'vue'

const checkList = ref(['Option A'])
const options = [
  { value: 'Option A', label: 'Option A' },
  { value: 'Option B', label: 'Option B' },
  { value: 'Option C', label: 'Option C', disabled: true }
]
</script>
```

## 不确定状态全

```vue
<template>
  <el-checkbox
    v-model="checkAll"
    :indeterminate="isIndeterminate"
    @change="handleCheckAllChange"
  >
    Check all
  </el-checkbox>
  <el-checkbox-group v-model="checkedCities" @change="handleCheckedCitiesChange">
    <el-checkbox v-for="city in cities" :key="city" :value="city">
      {{ city }}
    </el-checkbox>
  </el-checkbox-group>
</template>

<script setup>
import { ref } from 'vue'

const checkAll = ref(false)
const isIndeterminate = ref(true)
const cities = ['Shanghai', 'Beijing', 'Guangzhou', 'Shenzhen']
const checkedCities = ref(['Shanghai', 'Beijing'])

const handleCheckAllChange = (val) => {
  checkedCities.value = val ? cities : []
  isIndeterminate.value = false
}

const handleCheckedCitiesChange = (value) => {
  const checkedCount = value.length
  checkAll.value = checkedCount === cities.length
  isIndeterminate.value = checkedCount > 0 && checkedCount < cities.length
}
</script>
```

## 最最大项
```vue
<template>
  <el-checkbox-group v-model="checkedCities" :min="1" :max="2">
    <el-checkbox v-for="city in cities" :key="city" :value="city">
      {{ city }}
    </el-checkbox>
  </el-checkbox-group>
</template>
```

## 按钮样式

```vue
<template>
  <el-checkbox-group v-model="checkList">
    <el-checkbox-button value="Option A">Option A</el-checkbox-button>
    <el-checkbox-button value="Option B">Option B</el-checkbox-button>
    <el-checkbox-button value="Option C">Option C</el-checkbox-button>
  </el-checkbox-group>
</template>
```

## 带边
```vue
<template>
  <el-checkbox v-model="checked" border>Option</el-checkbox>
</template>
```

## API 参考
### Checkbox 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string \| number \| boolean` | |
| value | checkbox-group 中使用时| `string \| number \| boolean \| object` | |
| label | 标签文本 | `string \| number \| boolean \| object` | |
| true-value | 选中时| `string \| number` | |
| false-value | 未选中时| `string \| number` | |
| disabled | 是否禁用 | `boolean` | `false` |
| border | 是否添加边框 | `boolean` | `false` |
| size | 尺寸 | `'large' \| 'default' \| 'small'` | |
| name | 原生 name 属性| `string` | |
| checked | 是否选中 | `boolean` | `false` |
| indeterminate | 不确定状态| `boolean` | `false` |
| validate-event | 是否触发表单验证 | `boolean` | `true` |

### Checkbox 事件

| Name | Description | Type |
|------|-------------|------|
| change | 当值改变时触发 | `(value: string \| number \| boolean) => void` |

### CheckboxGroup 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string[] \| number[]` | `[]` |
| size | 复选框尺寸 | `'large' \| 'default' \| 'small'` | |
| disabled | 是否禁用 | `boolean` | `false` |
| min | 最小选中项数 | `number` | |
| max | 最大选中项数 | `number` | |
| options | 选项数据 | `Array<{[key: string]: any}>` | |
| props | 配置选项 | `object` | |
| type | 组件类型 | `'checkbox' \| 'button'` | `'checkbox'` |

## 最佳实践
1. 2.6.0 起使`value` 属性而非 `label`)作为复选框2. 使用 `indeterminate` 实现全选模3. 使用 `min`/`max` 限制选择数量
