---
name: "el-autocomplete"
description: "用于输入建议自动完成组件。当用户需要提供输入建议、自动完成功能即时搜索功能时调用
---

# Autocomplete 组件

Autocomplete 根据用户输入提供输入建议
## 何时使用

- 搜索建议
- 表单自动完成
- 地址输入
- 从选项中快速选择

## 基本用法

```vue
<template>
  <el-autocomplete
    v-model="state"
    :fetch-suggestions="querySearch"
    clearable
    placeholder="Please input"
    @select="handleSelect"
  />
</template>

<script setup>
import { ref, onMounted } from 'vue'

const state = ref('')
const links = ref([])

const querySearch = (queryString, cb) => {
  const results = queryString
    ? links.value.filter(createFilter(queryString))
    : links.value
  cb(results)
}

const createFilter = (queryString) => {
  return (restaurant) => {
    return (
      restaurant.value.toLowerCase().indexOf(queryString.toLowerCase()) === 0
    )
  }
}

const handleSelect = (item) => {
  console.log(item)
}

onMounted(() => {
  links.value = [
    { value: 'vue', link: 'https://github.com/vuejs/vue' },
    { value: 'element', link: 'https://github.com/ElemeFE/element' },
    { value: 'cooking', link: 'https://github.com/ElemeFE/cooking' }
  ]
})
</script>
```

## 自定义模
```vue
<template>
  <el-autocomplete
    v-model="state"
    :fetch-suggestions="querySearch"
    placeholder="Please input"
  >
    <template #default="{ item }">
      <div class="value">{{ item.value }}</div>
      <span class="link">{{ item.link }}</span>
    </template>
  </el-autocomplete>
</template>
```

## 远程搜索

```vue
<template>
  <el-autocomplete
    v-model="state"
    :fetch-suggestions="querySearchAsync"
    placeholder="Please input"
  />
</template>

<script setup>
import { ref } from 'vue'

const state = ref('')

const querySearchAsync = (queryString, cb) => {
  fetch(`/api/search?q=${queryString}`)
    .then(res => res.json())
    .then(data => cb(data))
}
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定| `string` | |
| placeholder | 占位| `string` | |
| disabled | 是否禁用 | `boolean` | `false` |
| value-key | 显示值| `string` | `'value'` |
| debounce | 防抖延迟(毫秒) | `number` | `300` |
| placement | 弹出位置 | Placement enum | `'bottom-start'` |
| fetch-suggestions | 获取建议方| `(queryString, cb) => void` | |
| popper-class | 自定义弹出类 | `string` | |
| trigger-on-focus | 聚焦时触| `boolean` | `true` |
| name | 原生 name 属性| `string` | |
| select-when-unmatched | 即使没有匹配也按回车选择 | `boolean` | `false` |
| label | 标签文本 | `string` | |
| hide-loading | 隐藏加载图标 | `boolean` | `false` |
| popper-append-to-body | 将弹出附加到 body | `boolean` | `true` |
| highlight-first-item | 高亮第一| `boolean` | `false` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| select | 选择建议时触| `(item) => void` |
| change | 输入值改变时触发 | `(value: string) => void` |
| input | 输入时触| `(value: string) => void` |
| focus | 聚焦时触| `(event: Event) => void` |
| blur | 失焦时触| `(event: Event) => void` |
| clear | 清除时触| `() => void` |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| default | 自定义建议项 | `{ item }` |
| prefix | 前缀内容 | |
| suffix | 后缀内容 | |
| prepend | 前置内容 | |
| append | 后置内容 | |

## 最佳实践
1. 使用 `debounce` 减少 API 调用
2. 实现 `fetch-suggestions` 用于异步数据
3. 使用自定义模板用于丰富建