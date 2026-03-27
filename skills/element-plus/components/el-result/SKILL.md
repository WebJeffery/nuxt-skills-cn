---
name: "el-result"
description: "用于操作结果反馈结果组件。当用户需要显示成功、错误、警告信息结果页面以及自定义内容时调用---

# Result 组件

Result 组件提供操作结果访问异常反馈
## 何时使用

- 成功页面
- 错误页面
- 警告显示
- 信息结果

## 基本用法

```vue
<template>
  <el-result icon="success" title="Success" sub-title="Operation completed" />
  <el-result icon="warning" title="Warning" sub-title="Please check your input" />
  <el-result icon="error" title="Error" sub-title="Something went wrong" />
  <el-result icon="info" title="Info" sub-title="Additional information" />
</template>
```

## 自定义内容
```vue
<template>
  <el-result
    icon="error"
    title="404"
    sub-title="Sorry, page you visited does not exist."
  >
    <template #extra>
      <el-button type="primary" @click="goHome">Back Home</el-button>
    </template>
  </el-result>
</template>

<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()
const goHome = () => router.push('/')
</script>
```

## 自定义图
```vue
<template>
  <el-result title="Custom Icon">
    <template #icon>
      <el-icon :size="100"><Warning /></el-icon>
    </template>
  </el-result>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| title | 标题 | `string` | `''` |
| sub-title | 副标签| `string` | `''` |
| icon | 图标类型 | `'primary' \| 'success' \| 'warning' \| 'info' \| 'error'` | `'info'` |

### 插槽

| Name | Description |
|------|-------------|
| icon | 自定义图|
| title | 自定义标签|
| sub-title | 自定义副标题 |
| extra | 额外区域内容 |

## 最佳实践
1. 根据上下文使用适当图标类型2. 在额外插槽中提供可操作按钮3. 保持消息清晰简