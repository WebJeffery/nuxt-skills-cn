---
name: "el-tag"
description: "用于标记和选择标签组件。当用户需要显示标签、类别用于过滤和分类型可移除标签时调用---

# Tag 组件

Tag 组件用于标记和选择,具有各种样式和主题
## 何时使用

- 类别标签
- 状态标签- 可移除过滤- 选择标记

## 基本用法

```vue
<template>
  <el-tag>Tag 1</el-tag>
  <el-tag type="success">Tag 2</el-tag>
  <el-tag type="info">Tag 3</el-tag>
  <el-tag type="warning">Tag 4</el-tag>
  <el-tag type="danger">Tag 5</el-tag>
</template>
```

## 可移除标签
```vue
<template>
  <el-tag
    v-for="tag in tags"
    :key="tag.name"
    closable
    @close="handleClose(tag)"
  >
    {{ tag.name }}
  </el-tag>
</template>

<script setup>
import { ref } from 'vue'

const tags = ref([
  { name: 'Tag 1' },
  { name: 'Tag 2' },
  { name: 'Tag 3' }
])

const handleClose = (tag) => {
  tags.value.splice(tags.value.indexOf(tag), 1)
}
</script>
```

## 尺寸

```vue
<template>
  <el-tag size="large">Large</el-tag>
  <el-tag>Default</el-tag>
  <el-tag size="small">Small</el-tag>
</template>
```

## 主题

```vue
<template>
  <el-tag effect="dark">Dark</el-tag>
  <el-tag effect="light">Light</el-tag>
  <el-tag effect="plain">Plain</el-tag>
</template>
```

## 圆角

```vue
<template>
  <el-tag round>Round Tag</el-tag>
</template>
```

## 可选中标签

```vue
<template>
  <el-check-tag :checked="checked" @change="onChange">Toggle Tag</el-check-tag>
</template>

<script setup>
import { ref } from 'vue'

const checked = ref(false)

const onChange = (value) => {
  checked.value = value
}
</script>
```

## API 参考
### Tag 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| type | 标签类型 | `'primary' \| 'success' \| 'info' \| 'warning' \| 'danger'` | `'primary'` |
| closable | 是否可移| `boolean` | `false` |
| disable-transitions | 禁用动画 | `boolean` | `false` |
| hit | 高亮边框 | `boolean` | `false` |
| color | 背景颜色 | `string` | |
| size | 尺寸 | `'large' \| 'default' \| 'small'` | |
| effect | 主题 | `'dark' \| 'light' \| 'plain'` | `'light'` |
| round | 是否圆角 | `boolean` | `false` |

### Tag 事件

| Name | Description | Type |
|------|-------------|------|
| click | 点击时触| `(evt: MouseEvent) => void` |
| close | 移除时触| `(evt: MouseEvent) => void` |

### CheckTag 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| checked / v-model:checked | 是否选中 | `boolean` | `false` |
| disabled | 是否禁用 | `boolean` | `false` |
| type | CheckTag 类型 | `'primary' \| 'success' \| 'info' \| 'warning' \| 'danger'` | `'primary'` |

### CheckTag 事件

| Name | Description | Type |
|------|-------------|------|
| change | 点击时触| `(value: boolean) => void` |

## 最佳实践
1. 使用 `closable` 用于可移除标签
2. 使用 `effect` 获得不同视觉样式3. 使用 `el-check-tag` 用于可选择标签