---
name: "el-select-v2"
description: "用于大数据集虚拟化选择组件。当用户需要从数千个选项中选择并使用虚拟滚动以提高性能时调用---

# 虚拟化选择(SelectV2)组件

SelectV2 提供虚拟化选择,用于高效处理大数据集
## 何时使用

- 大数据集(1000+ 选项)
- 性能关键选择
- 需要虚拟滚- 带有许多选项多
## 基本用法

```vue
<template>
  <el-select-v2 v-model="value" :options="options" placeholder="Select" />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
const options = Array.from({ length: 1000 }).map((_, idx) => ({
  value: `value${idx}`,
  label: `Option ${idx}`
}))
</script>
```

## 多
```vue
<template>
  <el-select-v2
    v-model="value"
    :options="options"
    multiple
    collapse-tags
    collapse-tags-tooltip
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref([])
const options = Array.from({ length: 1000 }).map((_, idx) => ({
  value: `value${idx}`,
  label: `Option ${idx}`
}))
</script>
```

## 可过
```vue
<template>
  <el-select-v2
    v-model="value"
    :options="options"
    filterable
  />
</template>
```

## 远程搜索

```vue
<template>
  <el-select-v2
    v-model="value"
    :options="options"
    filterable
    remote
    :remote-method="remoteMethod"
    :loading="loading"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
const options = ref([])
const loading = ref(false)

const remoteMethod = async (query) => {
  if (query) {
    loading.value = true
    const res = await fetch(`/api/search?q=${query}`)
    options.value = await res.json()
    loading.value = false
  }
}
</script>
```

## 分组选项

```vue
<template>
  <el-select-v2 v-model="value" :options="groupedOptions" />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
const groupedOptions = [
  {
    label: 'Group 1',
    options: [
      { value: '1', label: 'Option 1' },
      { value: '2', label: 'Option 2' }
    ]
  }
]
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string \| number \| boolean \| object \| array` | |
| options | 选项数据 | `array` | |
| props | 配置选项 | `object` | |
| multiple | 启用多| `boolean` | `false` |
| disabled | 禁用 | `boolean` | `false` |
| clearable | 可清| `boolean` | `false` |
| filterable | 可过| `boolean` | `false` |
| remote | 远程搜索 | `boolean` | `false` |
| remote-method | 远程搜索方法 | `(query: string) => void` | |
| loading | 加载状态| `boolean` | `false` |
| height | 下拉高度 | `number` | `274` |
| item-height | 项目高度 | `number` | `34` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值改| `(val) => void` |
| visible-change | 下拉可见性改| `(visible: boolean) => void` |
| remove-tag | 标签移除 | `(tagValue) => void` |
| clear | 清除点击 | `() => void` |
| blur | 输入失焦 | `(e: FocusEvent) => void` |
| focus | 输入聚焦 | `(e: FocusEvent) => void` |

### 插槽

| Name | Description |
|------|-------------|
| default | 自定义选项渲染|
| header | 下拉页眉 |
| footer | 下拉页脚 |
| empty | 空内容|
| prefix | 输入前缀 |
| tag | 自定义标签|

### 暴露

| Name | Description | Type |
|------|-------------|------|
| focus | 聚焦输入 | `() => void` |
| blur | 失焦输入 | `() => void` |
| selectedLabel | 选中标签| `ComputedRef<string \| string[]>` |

## 最佳实践
1. 用于具有 1000+ 选项数据集
2. 使用 `height` `item-height` 进行性能调优
3. 使用 `remote` 用于服务器端过滤
