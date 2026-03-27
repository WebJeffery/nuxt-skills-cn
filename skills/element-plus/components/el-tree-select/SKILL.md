---
name: "el-tree-select"
description: "结合树和选择功能树选择组件。当用户需要在下拉列表中选择分层数据,如组织结构类别树时调用---

# TreeSelect 组件

TreeSelect 结合了树和选择组件,用于分层数据选择
## 何时使用

- 组织结构选择
- 类别树选择
- 分层数据选择
- 基于树下拉列表

## 基本用法

```vue
<template>
  <el-tree-select
    v-model="value"
    :data="data"
    placeholder="Select"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref()
const data = [
  {
    value: '1',
    label: 'Level 1',
    children: [
      { value: '1-1', label: 'Level 1-1' },
      { value: '1-2', label: 'Level 1-2' }
    ]
  }
]
</script>
```

## 选择任意级别

```vue
<template>
  <el-tree-select
    v-model="value"
    :data="data"
    check-strictly
  />
</template>
```

## 多
```vue
<template>
  <el-tree-select
    v-model="value"
    :data="data"
    multiple
    show-checkbox
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref([])
</script>
```

## 可过
```vue
<template>
  <el-tree-select
    v-model="value"
    :data="data"
    filterable
  />
</template>
```

## 延迟加载

```vue
<template>
  <el-tree-select
    v-model="value"
    :props="props"
    lazy
    :load="load"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref()
const props = { label: 'name', children: 'zones' }

const load = (node, resolve) => {
  if (node.level === 0) {
    return resolve([{ name: 'Root' }])
  }
  setTimeout(() => {
    resolve([{ name: 'Child' }])
  }, 500)
}
</script>
```

## API 参考
### 属性
继承自树和选择组件
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string \| number \| array` | |
| data | 树数| `array` | |
| multiple | 多| `boolean` | `false` |
| check-strictly | 父子独立 | `boolean` | `false` |
| filterable | 启用过滤 | `boolean` | `false` |
| lazy | 启用延迟加载 | `boolean` | `false` |
| load | 延迟加载方法 | `(node, resolve) => void` | |
| cache-data | 缓存延迟节点数据 | `CacheOption[]` | `[]` |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| treeRef | 树组件实现| `TreeInstance` |
| selectRef | 选择组件实现例 | `SelectInstance` |

## 最佳实践
1. 使用 `check-strictly` 用于选择任意级别
2. 使用 `lazy` 用于大型树数3. 使用 `cache-data` 用于延迟加载标签