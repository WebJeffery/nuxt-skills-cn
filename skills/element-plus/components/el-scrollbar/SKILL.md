---
name: "el-scrollbar"
description: "用于自定义滚动条滚动条组件。当用户需要替换原生滚动条、创建自定义滚动区域实现无限滚动时调用---

# Scrollbar 组件

Scrollbar 用可自定义替代方案替换原生浏览器滚动条
## 何时使用

- 自定义滚动条样式
- 可滚动容- 无限滚动
- 自定义滚动区
## 基本用法

```vue
<template>
  <el-scrollbar height="400px">
    <p v-for="item in 20" :key="item">{{ item }}</p>
  </el-scrollbar>
</template>
```

## 水平滚动

```vue
<template>
  <el-scrollbar>
    <div style="display: flex; width: 1000px;">
      <div v-for="item in 20" :key="item" style="width: 100px;">{{ item }}</div>
    </div>
  </el-scrollbar>
</template>
```

## 最大高
```vue
<template>
  <el-scrollbar max-height="400px">
    <p v-for="item in 50" :key="item">{{ item }}</p>
  </el-scrollbar>
</template>
```

## 手动滚动

```vue
<template>
  <el-scrollbar ref="scrollbarRef" height="400px">
    <p v-for="item in 100" :key="item">{{ item }}</p>
  </el-scrollbar>
  <el-button @click="scrollToTop">Scroll to Top</el-button>
</template>

<script setup>
import { ref } from 'vue'

const scrollbarRef = ref()

const scrollToTop = () => {
  scrollbarRef.value.setScrollTop(0)
}
</script>
```

## 无限滚动

```vue
<template>
  <el-scrollbar height="400px" @end-reached="loadMore">
    <p v-for="item in items" :key="item">{{ item }}</p>
  </el-scrollbar>
</template>

<script setup>
import { ref } from 'vue'

const items = ref(Array.from({ length: 20 }, (_, i) => i + 1))

const loadMore = (direction) => {
  if (direction === 'bottom') {
    const newItems = Array.from({ length: 10 }, (_, i) => items.value.length + i + 1)
    items.value.push(...newItems)
  }
}
</script>
```

## 始终可见

```vue
<template>
  <el-scrollbar height="400px" always>
    <p v-for="item in 50" :key="item">{{ item }}</p>
  </el-scrollbar>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| height | 滚动条高| `string \| number` | |
| max-height | 最大高| `string \| number` | |
| native | 使用原生滚动| `boolean` | `false` |
| wrap-style | 包装容器样式 | `string \| CSSProperties` | |
| wrap-class | 包装容器| `string` | |
| view-style | 视图样式 | `string \| CSSProperties` | |
| view-class | 视图| `string` | |
| noresize | 忽略容器大小更改 | `boolean` | `false` |
| tag | 视图元素标签 | `string` | `'div'` |
| always | 始终显示滚动| `boolean` | `false` |
| min-size | 最小滚动条尺寸 | `number` | `20` |
| distance | 触发 end-reached 距| `number` | `0` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| scroll | 滚动时触| `({ scrollLeft, scrollTop }) => void` |
| end-reached | 滚动结束时触| `(direction: 'top' \| 'bottom' \| 'left' \| 'right') => void` |

### 插槽

| Name | Description |
|------|-------------|
| default | 可滚动内容|

### 暴露

| Name | Description | Type |
|------|-------------|------|
| scrollTo | 滚动到位| `(options: ScrollToOptions \| number, yCoord?: number) => void` |
| setScrollTop | 设置滚动顶部 | `(scrollTop: number) => void` |
| setScrollLeft | 设置滚动左侧 | `(scrollLeft: number) => void` |
| update | 更新滚动条状态| `() => void` |
| wrapRef | 包装元素引用 | `Ref<HTMLDivElement>` |

## 最佳实践
1. 使用 `height` 用于固定高度容器
2. 使用 `max-height` 用于灵活容器
3. 使用 `always` 以在触摸设备上获得更好可见4. 使用 `end-reached` 用于无限滚动
