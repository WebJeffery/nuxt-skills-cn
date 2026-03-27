---
name: "el-alert"
description: "用于显示重要警告消息警告组件。当用户需要显示不可关闭通知、状态消息需要用户注意重要信息时调用
---

# Alert 组件

Alert 组件在页面上显示重要警告消息。与通知不同,Alert 是非覆盖元素,不会自动消失
## 何时使用

- 显示重要系统消息警告
- 显示操作结果(success、error、warning、info)
- 展示不可关闭状态信- 用重要说明引导用
## 基本用法

```vue
<template>
  <el-alert title="success alert" type="success" />
  <el-alert title="warning alert" type="warning" />
  <el-alert title="info alert" type="info" />
  <el-alert title="error alert" type="error" />
  <el-alert title="primary alert" type="primary" />
</template>
```

## 主题变化

```vue
<template>
  <el-alert title="Light theme" type="success" effect="light" />
  <el-alert title="Dark theme" type="success" effect="dark" />
</template>
```

## 可自定义关闭按钮

```vue
<template>
  <el-alert title="unclosable alert" type="success" :closable="false" />
  <el-alert title="custom close text" type="info" close-text="Got it!" />
  <el-alert title="with close callback" type="warning" @close="handleClose" />
</template>

<script setup>
const handleClose = (event) => {
  console.log('Alert closed', event)
}
</script>
```

### 带图
```vue
<template>
  <el-alert title="with default icon" type="success" show-icon />
  <el-alert title="custom icon slot" type="warning" show-icon>
    <template #icon>
      <el-icon><Star /></el-icon>
    </template>
  </el-alert>
</template>
```

### 居中文本

```vue
<template>
  <el-alert title="centered alert" type="success" center />
</template>
```

### 带描
```vue
<template>
  <el-alert
    title="with description"
    type="success"
    description="This is a description message with more details."
  />
</template>
```

### 包含所有功能完整示例

```vue
<template>
  <el-alert
    title="Success Alert"
    type="success"
    description="Your operation was completed successfully."
    show-icon
    center
    closable
    close-text="Dismiss"
    @close="handleClose"
  />
</template>

<script setup>
const handleClose = (event) => {
  console.log('Alert dismissed')
}
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| title | Alert 标题(必需) | `string` | |
| type | Alert 类型 | `'primary' \| 'success' \| 'warning' \| 'info' \| 'error'` | `'info'` |
| description | 描述文本 | `string` | |
| closable | Alert 是否可以关闭 | `boolean` | `true` |
| center | 内容是否居中 | `boolean` | `false` |
| close-text | 自定义关闭按钮文| `string` | |
| show-icon | 是否显示类型图标 | `boolean` | `false` |
| effect | 主题样式 | `'light' \| 'dark'` | `'light'` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| close | Alert 关闭时触| `(event: MouseEvent) => void` |

### 插槽

| Name | Description |
|------|-------------|
| default | Alert 描述内容|
| title | Alert 标题内容|
| icon | Alert 图标内容|

## 常见模式

### 动Alert 类型

```vue
<template>
  <el-alert
    :title="alertConfig.title"
    :type="alertConfig.type"
    :description="alertConfig.description"
    show-icon
  />
</template>

<script setup>
import { ref, reactive } from 'vue'

const alertConfig = reactive({
  title: 'Operation Status',
  type: 'info',
  description: ''
})

const showSuccess = () => {
  alertConfig.type = 'success'
  alertConfig.title = 'Success!'
  alertConfig.description = 'Operation completed successfully.'
}

const showError = () => {
  alertConfig.type = 'error'
  alertConfig.title = 'Error!'
  alertConfig.description = 'An error occurred during operation.'
}
</script>
```

### 自动关闭 Alert

```vue
<template>
  <el-alert
    v-if="visible"
    title="Auto-dismiss Alert"
    type="success"
    @close="visible = false"
  />
</template>

<script setup>
import { ref, watch } from 'vue'

const visible = ref(true)

watch(visible, (newVal) => {
  if (newVal) {
    setTimeout(() => {
      visible.value = false
    }, 3000)
  }
})
</script>
```

## 组件交互

- **Form** 组件配合使用以显示验证结- **Message** 结合使用,用于临时通知与持Alert 对- **Notification** 配合使用,用于自动关闭基于角落 Alert
- **Dialog** 集成,用于模Alert 场景

## 最佳实践
1. 使用适当类型 `success` 用于积极结果,`error` 用于失败,`warning` 用于警告,`info` 用于中性信2. 保持标题简描述信息丰富
3. 使用 `show-icon` 以获得更好视觉识别
4. 对于深色主题界面,考虑使用 `effect="dark"`
5. 对于必须确认关Alert,使用 `closable="false"`
