---
name: "el-input-tag"
description: "用于将内容添加为标签输入标签组件。当用户需要创建标签输入、多值输入或关键字/标签输入字段时调用。"
---

# InputTag 组件

InputTag 允许用户将内容添加为标签，具有各种自定义选项。

## 何时使用

- 标签输入
- 关键字输入
- 多值输入
- 邮件/标签列表

## 基本用法

```vue
<template>
  <el-input-tag v-model="tags" placeholder="按 Enter 添加标签" />
</template>

<script setup>
import { ref } from 'vue'

const tags = ref(['标签 1', '标签 2'])
</script>
```

## 自定义触发器

```vue
<template>
  <el-input-tag
    v-model="tags"
    trigger="Space"
    placeholder="按空格添加标签"
  />
</template>
```

## 最大标签数

```vue
<template>
  <el-input-tag v-model="tags" :max="5" />
</template>
```

## 折叠标签

```vue
<template>
  <el-input-tag
    v-model="tags"
    collapse-tags
    collapse-tags-tooltip
  />
</template>
```

## 可拖动

```vue
<template>
  <el-input-tag v-model="tags" draggable />
</template>
```

## 分隔符

```vue
<template>
  <el-input-tag v-model="tags" delimiter="," />
</template>
```

## 自定义标签

```vue
<template>
  <el-input-tag v-model="tags">
    <template #tag="{ value, index }">
      <el-tag type="success">{{ value }}</el-tag>
    </template>
  </el-input-tag>
</template>
```

## API 参考

### 属性

| Name | Description | Type | Default |
|------|-------------|------|---------|
| model-value / v-model | 绑定值 | `string[]` | `[]` |
| max | 最大标签数 | `number` | - |
| tag-type | 标签类型 | `'' \| 'success' \| 'info' \| 'warning' \| 'danger'` | `'info'` |
| tag-effect | 标签效果 | `'' \| 'light' \| 'dark' \| 'plain'` | `'light'` |
| trigger | 触发键 | `'Enter' \| 'Space'` | `'Enter'` |
| draggable | 启用拖动 | `boolean` | `false` |
| delimiter | 分隔符匹配时添加标签 | `string \| RegExp` | - |
| size | 尺寸 | `'large' \| 'default' \| 'small'` | - |
| collapse-tags | 折叠标签 | `boolean` | `false` |
| collapse-tags-tooltip | 折叠时显示工具提示 | `boolean` | `false` |
| save-on-blur | 失焦时保存 | `boolean` | `true` |
| clearable | 显示清除按钮 | `boolean` | `false` |
| disabled | 禁用 | `boolean` | `false` |
| placeholder | 占位符 | `string` | - |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 值变化 | `(value: string[]) => void` |
| add-tag | 标签添加 | `(value: string \| string[]) => void` |
| remove-tag | 标签移除 | `(value: string, index: number) => void` |
| focus | 输入聚焦 | `(event: FocusEvent) => void` |
| blur | 输入失焦 | `(event: FocusEvent) => void` |
| clear | 清除点击 | `() => void` |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| tag | 自定义标签 | `{ value, index }` |
| prefix | 前缀内容 | - |
| suffix | 后缀内容 | - |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| focus | 聚焦输入 | `() => void` |
| blur | 失焦输入 | `() => void` |

## 最佳实践

1. 使用 `max` 限制标签数量
2. 使用 `delimiter` 进行逗号分隔输入
3. 使用 `draggable` 进行重新排序
