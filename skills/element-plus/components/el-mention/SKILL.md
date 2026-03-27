---
name: "el-mention"
description: "用于在输入中提及用户或项目提及组件。当用户需要实现@提及、用户标签或在文本输入中引用项目时调用。"
---

# Mention 组件

Mention 允许用户在输入字段中提及某人或某物。

## 何时使用

- 用户提及(@username)
- 项目引用
- 标签功能
- 富文本输入

## 基本用法

```vue
<template>
  <el-mention
    v-model="value"
    :options="options"
    placeholder="输入 @ 进行提及"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
const options = [
  { value: 'user1', label: '用户 1' },
  { value: 'user2', label: '用户 2' },
  { value: 'user3', label: '用户 3' }
]
</script>
```

## 文本区域模式

```vue
<template>
  <el-mention
    v-model="value"
    :options="options"
    type="textarea"
    placeholder="输入 @ 进行提及"
  />
</template>
```

## 自定义属性

```vue
<template>
  <el-mention
    v-model="value"
    :options="options"
    :props="{ value: 'id', label: 'name' }"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
const options = [
  { id: '1', name: '用户 1' },
  { id: '2', name: '用户 2' }
]
</script>
```

## 自定义标签

```vue
<template>
  <el-mention v-model="value" :options="options">
    <template #label="{ item }">
      <div style="display: flex; align-items: center;">
        <el-avatar :size="24" />
        <span>{{ item.label }}</span>
      </div>
    </template>
  </el-mention>
</template>
```

## 远程加载

```vue
<template>
  <el-mention
    v-model="value"
    :options="options"
    :loading="loading"
    @search="handleSearch"
  />
</template>

<script setup>
import { ref } from 'vue'

const value = ref('')
const options = ref([])
const loading = ref(false)

const handleSearch = async (pattern, prefix) => {
  loading.value = true
  const res = await fetch(`/api/users?q=${pattern}`)
  options.value = await res.json()
  loading.value = false
}
</script>
```

## 自定义触发器

```vue
<template>
  <el-mention
    v-model="value"
    :options="options"
    prefix="#"
    placeholder="输入 # 进行标签"
  />
</template>
```

## 整体删除

```vue
<template>
  <el-mention
    v-model="value"
    :options="options"
    whole
  />
</template>
```

## API 参考

### 属性

| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 输入值 | `string` | `''` |
| options | 提及选项 | `MentionOption[]` | `[]` |
| props | 配置选项 | `MentionOptionProps` | `{value: 'value', label: 'label', disabled: 'disabled'}` |
| prefix | 触发字符 | `string \| string[]` | `'@'` |
| split | 分隔字符 | `string` | `' '` |
| filter-option | 自定义过滤逻辑 | `(pattern, option) => boolean` | - |
| placement | 弹出位置 | `'bottom' \| 'top'` | `'bottom'` |
| show-arrow | 显示下拉箭头 | `boolean` | `false` |
| offset | 下拉偏移 | `number` | `0` |
| whole | 整体删除提及 | `boolean` | `false` |
| check-is-whole | 自定义整体检查 | `(pattern, prefix) => boolean` | - |
| loading | 加载状态 | `boolean` | `false` |
| popper-class | 自定义下拉类 | `string \| object` | `''` |
| popper-style | 自定义下拉样式 | `string \| object` | - |

### 事件

| Name | Description | Type |
|------|-------------|------|
| search | 触发前缀时触发 | `(pattern, prefix) => void` |
| select | 选项被选中 | `(option, prefix) => void` |
| whole-remove | 整体提及被移除 | `(pattern, prefix) => void` |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| label | 自定义选项标签 | `{ item, index }` |
| loading | 加载内容 | - |
| header | 下拉头部 | - |
| footer | 下拉底部 | - |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| input | 输入组件实现例 | `Ref<InputInstance>` |
| tooltip | 工具提示组件实现例 | `Ref<TooltipInstance>` |
| dropdownVisible | 下拉可见性 | `ComputedRef<boolean>` |

## 最佳实践

1. 使用 `loading` 进行异步选项加载
2. 使用 `prefix` 用于不同触发字符
3. 使用 `whole` 在删除提及时获得更好用户体验
