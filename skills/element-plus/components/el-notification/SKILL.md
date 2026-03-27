---
name: "el-notification"
description: "用于在页面角落显示通知消息通知组件。当用户需要显示带有自定义内容持久通知时调用---

# Element Plus Notification 组件

在页面角落显示全局通知消息
## 何时调用

在以下情况下调用此技能
- 用户需要显示持久通知
- 用户想要显示带有标题和消息通知
- 用户需要在不同角落定位通知
- 用户想要自定义通知内容
- 用户询问带有 HTML VNode 通知

## 功能特性
- **多种类型**: success、warning、info、error
- **自定义位*: 右上、左上、右下、左- **可关*: 手动自动关- **自定义持续时*: 可配置显示时- **HTML 内容**: 支持 HTML 字符- **VNode 支持**: Vue 组件渲染为内容- **自定义图*: 自定义图标支- **偏移**: 距离屏幕边缘距
## API 参考
### Notification 选项

| Name | Description | Type | Default |
|------|-------------|------|---------|
| title | 标题 | `string` | '' |
| message | 描述文本 | `string \| VNode \| Function` | '' |
| dangerouslyUseHTMLString | 消息是否HTML | `boolean` | false |
| type | 通知类型 | `'primary' \| 'success' \| 'warning' \| 'info' \| 'error'` | '' |
| icon | 自定义图标组件| `string \| Component` | |
| customClass | 自定义类型| `string` | '' |
| duration | 关闭前持续时间(ms),0 = 不自动关| `number` | 4500 |
| position | 自定义位| `'top-right' \| 'top-left' \| 'bottom-right' \| 'bottom-left'` | top-right |
| showClose | 是否显示关闭按钮 | `boolean` | true |
| onClose | 关闭时回调 | `() => void` | |
| onClick | 点击时回调 | `() => void` | |
| offset | 距离屏幕边缘偏| `number` | 0 |
| appendTo | 通知附加到哪个元| `string \| HTMLElement` | |
| zIndex | 初始 zIndex | `number` | |

### Notification 方法

| Method | Description | Parameters | Return |
|--------|-------------|------------|--------|
| ElNotification | 显示通知 | `options` | Notification 实现例 |
| ElNotification.success | 显示成功通知 | `message \| options` | Notification 实现例 |
| ElNotification.warning | 显示警告通知 | `message \| options` | Notification 实现例 |
| ElNotification.info | 显示信息通知 | `message \| options` | Notification 实现例 |
| ElNotification.error | 显示错误通知 | `message \| options` | Notification 实现例 |
| ElNotification.closeAll | 关闭所有通知 | | |

### Notification 实现例

| Method | Description | Type |
|--------|-------------|------|
| close | 关闭通知 | `() => void` |

## 使用示例

### 基本用法

```vue
<template>
  <el-button @click="open">Show Notification</el-button>
</template>

<script setup>
import { ElNotification } from 'element-plus'

const open = () => {
  ElNotification({
    title: 'Title',
    message: 'This is a notification message'
  })
}
</script>
```

### 不同类型

```vue
<template>
  <el-button @click="openSuccess">Success</el-button>
  <el-button @click="openWarning">Warning</el-button>
  <el-button @click="openInfo">Info</el-button>
  <el-button @click="openError">Error</el-button>
</template>

<script setup>
import { ElNotification } from 'element-plus'

const openSuccess = () => {
  ElNotification.success({
    title: 'Success',
    message: 'This is a success message'
  })
}

const openWarning = () => {
  ElNotification.warning({
    title: 'Warning',
    message: 'This is a warning message'
  })
}

const openInfo = () => {
  ElNotification.info({
    title: 'Info',
    message: 'This is an info message'
  })
}

const openError = () => {
  ElNotification.error({
    title: 'Error',
    message: 'This is an error message'
  })
}
</script>
```

### 自定义位
```vue
<template>
  <el-button @click="openTopRight">Top Right</el-button>
  <el-button @click="openTopLeft">Top Left</el-button>
  <el-button @click="openBottomRight">Bottom Right</el-button>
  <el-button @click="openBottomLeft">Bottom Left</el-button>
</template>

<script setup>
import { ElNotification } from 'element-plus'

const openTopRight = () => {
  ElNotification({
    title: 'Top Right',
    message: 'Notification at top right',
    position: 'top-right'
  })
}

const openTopLeft = () => {
  ElNotification({
    title: 'Top Left',
    message: 'Notification at top left',
    position: 'top-left'
  })
}

const openBottomRight = () => {
  ElNotification({
    title: 'Bottom Right',
    message: 'Notification at bottom right',
    position: 'bottom-right'
  })
}

const openBottomLeft = () => {
  ElNotification({
    title: 'Bottom Left',
    message: 'Notification at bottom left',
    position: 'bottom-left'
  })
}
</script>
```

### 带偏
```vue
<template>
  <el-button @click="open">Notification with Offset</el-button>
</template>

<script setup>
import { ElNotification } from 'element-plus'

const open = () => {
  ElNotification({
    title: 'Offset',
    message: 'This notification has 100px offset',
    offset: 100
  })
}
</script>
```

### HTML 内容

```vue
<template>
  <el-button @click="open">HTML Notification</el-button>
</template>

<script setup>
import { ElNotification } from 'element-plus'

const open = () => {
  ElNotification({
    title: 'HTML Content',
    dangerouslyUseHTMLString: true,
    message: '<strong>This is <i>HTML</i> content</strong>'
  })
}
</script>
```

### VNode 内容

```vue
<template>
  <el-button @click="open">VNode Notification</el-button>
</template>

<script setup>
import { ElNotification, h } from 'element-plus'

const open = () => {
  ElNotification({
    title: 'VNode Content',
    message: h('i', { style: 'color: teal' }, 'This is VNode content')
  })
}
</script>
```

### 隐藏关闭按钮

```vue
<template>
  <el-button @click="open">No Close Button</el-button>
</template>

<script setup>
import { ElNotification } from 'element-plus'

const open = () => {
  ElNotification({
    title: 'No Close',
    message: 'This notification has no close button',
    showClose: false
  })
}
</script>
```

### 自定义持续时
```vue
<template>
  <el-button @click="open">Long Duration</el-button>
</template>

<script setup>
import { ElNotification } from 'element-plus'

const open = () => {
  ElNotification({
    title: 'Long Duration',
    message: 'This notification will stay for 10 seconds',
    duration: 10000
  })
}
</script>
```

### 手动关闭

```vue
<template>
  <el-button @click="open">Show</el-button>
  <el-button @click="close">Close</el-button>
</template>

<script setup>
import { ElNotification } from 'element-plus'

let notification = null

const open = () => {
  notification = ElNotification({
    title: 'Manual Close',
    message: 'Click to button to close',
    duration: 0  // No auto close
  })
}

const close = () => {
  if (notification) {
    notification.close()
    notification = null
  }
}
</script>
```

### 点击回调

```vue
<template>
  <el-button @click="open">Clickable Notification</el-button>
</template>

<script setup>
import { ElNotification } from 'element-plus'

const open = () => {
  ElNotification({
    title: 'Click Me',
    message: 'Click this notification to trigger callback',
    onClick: () => {
      console.log('Notification clicked!')
    }
  })
}
</script>
```

### 关闭所
```vue
<template>
  <el-button @click="openMultiple">Show Multiple</el-button>
  <el-button @click="closeAll">Close All</el-button>
</template>

<script setup>
import { ElNotification } from 'element-plus'

const openMultiple = () => {
  ElNotification.success({ title: 'Success', message: 'Message 1' })
  ElNotification.warning({ title: 'Warning', message: 'Message 2' })
  ElNotification.info({ title: 'Info', message: 'Message 3' })
}

const closeAll = () => {
  ElNotification.closeAll()
}
</script>
```

## 常见问题

### 1. 通知不显
确保已导Element Plus 样式:

```js
import 'element-plus/dist/index.css'
```

### 2. 通知重叠

使用 `offset` 调整位置:

```js
ElNotification({ message: 'Message 1', offset: 0 })
ElNotification({ message: 'Message 2', offset: 100 })
```

### 3. 通知不关
检`duration` 设置。使`duration: 0` 进行手动关闭:

```js
const notification = ElNotification({ message: 'Manual close', duration: 0 })
// Later
notification.close()
```

## 最佳实践
1. **使用适当类型*: 适当使用 success、warning、error、info
2. **保持消息简*: 通知应该简3. **使用标题**: 始终包含有意义标题
4. **位置一*: 在整个应用中使用一致位置
5. **处理异步操作**: 为异步操作结果显示通知
6. **避免 HTML**: 出于安全考虑,优先使用 VNode 而不HTML 字符