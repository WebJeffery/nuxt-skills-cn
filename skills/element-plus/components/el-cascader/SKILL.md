---
name: "el-cascader"
description: "用于分层选项选择级联选择器组件。当用户需要从多级数据(如地区、分类型组织结构)中选择时调用---

# 级联选择器组件
级联选择器显示和选择具有清晰层次结构选项
## 何时使用

- 地区/位置选择
- 分类层次结构
- 组织结构
- 多级导航

## 基本用法

```vue
<template>
  <el-cascader v-model="value" :options="options" />
</template>

<script setup>
import { ref } from 'vue'

const value = ref([])
const options = [
  {
    value: 'guide',
    label: 'Guide',
    children: [
      { value: 'disciplines', label: 'Disciplines' },
      { value: 'consistency', label: 'Consistency' }
    ]
  }
]
</script>
```

## 多
```vue
<template>
  <el-cascader v-model="value" :options="options" :props="props" clearable />
</template>

<script setup>
import { ref } from 'vue'

const value = ref([])
const props = { multiple: true }
</script>
```

## 选择任意级别

```vue
<template>
  <el-cascader v-model="value" :options="options" :props="{ checkStrictly: true }" />
</template>
```

## 动态加
```vue
<template>
  <el-cascader :props="props" />
</template>

<script setup>
const props = {
  lazy: true,
  lazyLoad(node, resolve) {
    const { level } = node
    setTimeout(() => {
      const nodes = Array.from({ length: level + 1 }).map((item, i) => ({
        value: `${level}-${i}`,
        label: `Option ${level}-${i}`,
        leaf: level >= 2
      }))
      resolve(nodes)
    }, 1000)
  }
}
</script>
```

## 可搜
```vue
<template>
  <el-cascader v-model="value" :options="options" filterable />
</template>
```

## API 参考
### Cascader 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string \| number \| array` | |
| options | 选项数据 | `CascaderOption[]` | |
| props | 配置选项 | `CascaderProps` | |
| size | 输入框尺寸| `'large' \| 'default' \| 'small'` | |
| placeholder | 占位| `string` | |
| disabled | 是否禁用 | `boolean` | |
| clearable | 是否可清| `boolean` | |
| show-all-levels | 显示所有级| `boolean` | `true` |
| collapse-tags | 多选模式下折叠标签 | `boolean` | |
| separator | 选项标签分隔| `string` | `' / '` |
| filterable | 是否可搜| `boolean` | |

### CascaderProps

| Name | Description | Type | Default |
|------|-------------|------|---------|
| expandTrigger | 展开触发方式 | `'click' \| 'hover'` | `'click'` |
| multiple | 启用多| `boolean` | `false` |
| checkStrictly | 父子独立 | `boolean` | `false` |
| emitPath | 发出路径数组 | `boolean` | `true` |
| lazy | 启用懒加| `boolean` | `false` |
| lazyLoad | 懒加载方| `(node, resolve) => void` | |
| value | 值键 | `string` | `'value'` |
| label | 标签| `string` | `'label'` |
| children | 子节点键 | `string` | `'children'` |

### 事件

| Name | Description |
|------|-------------|
| change | 值改|
| expand-change | 展开改变 |
| blur | 失焦事件 |
| focus | 聚焦事件 |

## 最佳实践
1. 使用 `checkStrictly` 选择任意级别
2. 对大型数据集使用 `lazy` 加载
3. 使用 `filterable` 实现可搜索级联选择