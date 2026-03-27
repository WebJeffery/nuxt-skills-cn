---
name: "el-popconfirm"
description: "用于简单确认对话框气泡确认框组件。当用户需要在执行之前确认操作(如删除操作不可逆操时调用---

# Popconfirm 组件

Popconfirm 为元素点击操作提供简单确认对话框
## 何时使用

- 删除确认
- 操作确认
- 不可逆操作警- 快速确认对话框

## 基本用法

```vue
<template>
  <el-popconfirm title="Are you sure to delete this?">
    <template #reference>
      <el-button>Delete</el-button>
    </template>
  </el-popconfirm>
</template>
```

## 自定
```vue
<template>
  <el-popconfirm
    title="Are you sure?"
    confirm-button-text="Yes"
    cancel-button-text="No"
    @confirm="handleConfirm"
    @cancel="handleCancel"
  >
    <template #reference>
      <el-button type="danger">Delete</el-button>
    </template>
  </el-popconfirm>
</template>

<script setup>
const handleConfirm = () => {
  console.log('Confirmed')
}

const handleCancel = () => {
  console.log('Cancelled')
}
</script>
```

## 触发事件

```vue
<template>
  <el-popconfirm
    title="Delete this item?"
    @confirm="deleteItem"
  >
    <template #reference>
      <el-button type="danger">Delete</el-button>
    </template>
  </el-popconfirm>
</template>

<script setup>
const deleteItem = () => {
  // Delete logic
}
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| title | 标题 | `string` | |
| effect | 工具提示主题 | `'dark' \| 'light'` | `'light'` |
| confirm-button-text | 确认按钮文本 | `string` | |
| cancel-button-text | 取消按钮文本 | `string` | |
| confirm-button-type | 确认按钮类型 | Button type enum | `'primary'` |
| cancel-button-type | 取消按钮类型 | Button type enum | `'text'` |
| icon | 图标组件 | `string \| Component` | `QuestionFilled` |
| icon-color | 图标颜色 | `string` | `'#f90'` |
| hide-icon | 隐藏图标 | `boolean` | `false` |
| hide-after | 消失延迟(毫秒) | `number` | `200` |
| teleported | 传送到 body | `boolean` | `true` |
| width | 气泡确认框宽| `string \| number` | `150` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| confirm | 点击确认按钮 | `(e: MouseEvent) => void` |
| cancel | 点击取消按钮 | `(e: MouseEvent) => void` |

### 插槽

| Name | Description | Type |
|------|-------------|------|
| reference | 触发元素 | |
| actions | 自定义页| `{ confirm, cancel }` |

### 暴露

| Name | Description | Type |
|------|-------------|------|
| hide | 隐藏气泡确认| `() => void` |

## 最佳实践
1. 用于破坏性操作确2. 提供清晰确认消3. 使用适当按钮类型