---
name: "el-loading"
description: "用于在加载数据时显示动画加载组件。当用户需要显示加载状态、异步操作反馈数据获取指示器时调用---

# Loading 组件

Loading 组件通过指令服务在加载数据时显示动画
## 何时使用

- 数据加载状态- 异步操作反馈
- 表单提交
- 页面转换

## 指令用法

```vue
<template>
  <el-table v-loading="loading" :data="tableData">
    <el-table-column prop="name" label="Name" />
  </el-table>
</template>

<script setup>
import { ref } from 'vue'

const loading = ref(true)
const tableData = ref([])
</script>
```

## 自定
```vue
<template>
  <div
    v-loading="loading"
    element-loading-text="Loading..."
    element-loading-spinner="el-icon-loading"
    element-loading-background="rgba(0, 0, 0, 0.8)"
  >
    Content
  </div>
</template>
```

## 全屏

```vue
<template>
  <el-button @click="openLoading">Show Full Screen Loading</el-button>
</template>

<script setup>
import { ElLoading } from 'element-plus'

const openLoading = () => {
  const loading = ElLoading.service({
    lock: true,
    text: 'Loading...',
    background: 'rgba(0, 0, 0, 0.7)'
  })
  setTimeout(() => loading.close(), 2000)
}
</script>
```

## 服务用法

```vue
<script setup>
import { ElLoading } from 'element-plus'

const showLoading = () => {
  const instance = ElLoading.service({
    target: '.container',
    text: 'Loading...',
    spinner: 'el-icon-loading',
    background: 'rgba(255, 255, 255, 0.7)'
  })
  
  return instance
}
</script>
```

## API 参考
### 选项

| Name | Description | Type | Default |
|------|-------------|------|---------|
| target | 要覆盖 DOM 节点 | `string \| HTMLElement` | `document.body` |
| body | 附加body | `boolean` | `false` |
| fullscreen | 全屏模式 | `boolean` | `true` |
| lock | 锁定滚动 | `boolean` | `false` |
| text | 加载文本 | `string \| VNode` | |
| spinner | 自定义加载器| `string` | |
| background | 背景颜色 | `string` | |
| customClass | 自定义类 | `string` | |
| svg | 自定SVG | `string` | |
| svgViewBox | SVG viewBox | `string` | |

### 指令

| Name | Description | Type |
|------|-------------|------|
| v-loading | 显示加载 | `boolean \| LoadingOptions` |
| element-loading-text | 加载文本 | `string` |
| element-loading-spinner | 加载图标 | `string` |
| element-loading-background | 背景颜色 | `string` |

## 最佳实践
1. 使用 `v-loading` 指令进行组件级别加2. 使用 `ElLoading.service` 进行编程控制
3. 设置 `lock` 以在加载期间防止滚动
