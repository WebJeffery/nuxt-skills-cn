---
name: "el-message-box"
description: "用于模拟系统警报、确认和提示模态对话框消息框组件。当用户需要显示警报消息、确认操作提示用户输入时调用---

# MessageBox 组件

MessageBox 为警报、确认和提示提供模态对话框
## 何时使用

- 警报消息
- 操作确认
- 用户输入提示
- 关键通知

## 警报

```vue
<template>
  <el-button @click="open">Show Alert</el-button>
</template>

<script setup>
import { ElMessageBox } from 'element-plus'

const open = () => {
  ElMessageBox.alert('This is a message', 'Title', {
    confirmButtonText: 'OK',
    callback: (action) => {
      console.log(action)
    }
  })
}
</script>
```

## 确认

```vue
<template>
  <el-button @click="open">Show Confirm</el-button>
</template>

<script setup>
import { ElMessageBox } from 'element-plus'

const open = () => {
  ElMessageBox.confirm(
    'This will permanently delete file. Continue?',
    'Warning',
    {
      confirmButtonText: 'OK',
      cancelButtonText: 'Cancel',
      type: 'warning'
    }
  ).then(() => {
    console.log('Confirmed')
  }).catch(() => {
    console.log('Cancelled')
  })
}
</script>
```

## 提示

```vue
<template>
  <el-button @click="open">Show Prompt</el-button>
</template>

<script setup>
import { ElMessageBox } from 'element-plus'

const open = () => {
  ElMessageBox.prompt('Please input your email', 'Tip', {
    confirmButtonText: 'OK',
    cancelButtonText: 'Cancel',
    inputPattern: /[\w!#$%&'*+/=?^_`{|}~-]+(?:\.[\w!#$%&'*+/=?^_`{|}~-]+)*@(?:[\w](?:[\w-]*[\w])?\.)+[\w](?:[\w-]*[\w])?/,
    inputErrorMessage: 'Invalid Email'
  }).then(({ value }) => {
    console.log('Email:', value)
  }).catch(() => {
    console.log('Cancelled')
  })
}
</script>
```

## 自定
```vue
<template>
  <el-button @click="open">Custom MessageBox</el-button>
</template>

<script setup>
import { ElMessageBox } from 'element-plus'

const open = () => {
  ElMessageBox({
    title: 'Message',
    message: 'This is a custom message',
    showCancelButton: true,
    confirmButtonText: 'OK',
    cancelButtonText: 'Cancel',
    beforeClose: (action, instance, done) => {
      if (action === 'confirm') {
        instance.confirmButtonLoading = true
        setTimeout(() => {
          done()
          instance.confirmButtonLoading = false
        },1000)
      } else {
        done()
      }
    }
  })
}
</script>
```

## 居中

```vue
<template>
  <el-button @click="open">Centered</el-button>
</template>

<script setup>
import { ElMessageBox } from 'element-plus'

const open = () => {
  ElMessageBox.confirm(
    'This is a centered message',
    'Center',
    {
      center: true
    }
  )
}
</script>
```

## 可拖
```vue
<template>
  <el-button @click="open">Draggable</el-button>
</template>

<script setup>
import { ElMessageBox } from 'element-plus'

const open = () => {
  ElMessageBox.confirm(
    'This is a draggable message',
    'Draggable',
    {
      draggable: true
    }
  )
}
</script>
```

## API 参考
### 方法

| Method | Description | Parameters |
|--------|-------------|------------|
| ElMessageBox.alert | 显示警报对话| `(message, title, options)` |
| ElMessageBox.confirm | 显示确认对话| `(message, title, options)` |
| ElMessageBox.prompt | 显示提示对话| `(message, title, options)` |
| ElMessageBox.close | 关闭消息| |

### 选项

| Name | Description | Type | Default |
|------|-------------|------|---------|
| title | 标题 | `string` | `''` |
| message | 内容 | `string \| VNode` | |
| type | 消息类型 | `'primary' \| 'success' \| 'warning' \| 'info' \| 'error'` | |
| icon | 自定义图| `string \| Component` | |
| dangerouslyUseHTMLString | 将消息渲染为 HTML | `boolean` | `false` |
| customClass | 自定义类 | `string` | `''` |
| customStyle | 自定义样式| `CSSProperties` | `{}` |
| showClose | 显示关闭按钮 | `boolean` | `true` |
| showCancelButton | 显示取消按钮 | `boolean` | `false` |
| showConfirmButton | 显示确认按钮 | `boolean` | `true` |
| cancelButtonText | 取消按钮文本 | `string` | `'Cancel'` |
| confirmButtonText | 确认按钮文本 | `string` | `'OK'` |
| cancelButtonType | 取消按钮类型 | Button type enum | |
| confirmButtonType | 确认按钮类型 | Button type enum | `'primary'` |
| closeOnClickModal | 点击背景时关| `boolean` | `true` |
| closeOnPressEscape | ESC 关闭 | `boolean` | `true` |
| center | 居中内容 | `boolean` | `false` |
| draggable | 启用拖动 | `boolean` | `false` |
| beforeClose | 关闭前回调 | `(action, instance, done) => void` | |
| distinguishCancelAndClose | 区分取消与关| `boolean` | `false` |
| inputPlaceholder | 输入占位| `string` | `''` |
| inputPattern | 输入验证模式 | `RegExp` | |
| inputValidator | 输入验证| `(value) => boolean \| string` | |
| inputErrorMessage | 验证错误消息 | `string` | `'Illegal input'` |

## 最佳实践
1. 使用 `alert` 用于简单通知
2. 使用 `confirm` 用于操作确认
3. 使用 `prompt` 用于用户输入
4. 使用 `beforeClose` 用于异步操作
