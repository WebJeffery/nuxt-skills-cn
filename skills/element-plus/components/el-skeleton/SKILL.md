---
name: "el-skeleton"
description: "用于加载占位符骨架屏组件。当用户需要显示加载状态、内容占位符改善感知加载性能时调用---

# Skeleton 组件

Skeleton 组件在数据加载期间提供加载占位符,以获得更好用户体验
## 何时使用

- 加载状态- 内容占位- 改善感知性能
- 异步数据加载

## 基本用法

```vue
<template>
  <el-skeleton />
</template>
```

## 可配置行
```vue
<template>
  <el-skeleton :rows="5" animated />
</template>
```

## 动画

```vue
<template>
  <el-skeleton animated />
</template>
```

## 自定义模
```vue
<template>
  <el-skeleton animated>
    <template #template>
      <el-skeleton-item variant="image" style="width: 240px; height: 240px" />
      <div style="padding: 14px">
        <el-skeleton-item variant="h3" style="width: 50%" />
        <div style="display: flex; align-items: center; justify-content: space-between; margin-top: 16px;">
          <el-skeleton-item variant="text" style="width: 30%" />
          <el-skeleton-item variant="text" style="width: 30%" />
        </div>
      </div>
    </template>
  </el-skeleton>
</template>
```

## 加载状态
```vue
<template>
  <el-skeleton :loading="loading" animated>
    <template #default>
      <div class="content">Real content here</div>
    </template>
  </el-skeleton>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const loading = ref(true)

onMounted(() => {
  setTimeout(() => {
    loading.value = false
  }, 2000)
})
</script>
```

## 避免渲染跳动

```vue
<template>
  <el-skeleton :loading="loading" :throttle="500" animated>
    <template #default>
      <div>Content</div>
    </template>
  </el-skeleton>
</template>
```

## API 参考
### Skeleton 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| animated | 显示动画 | `boolean` | `false` |
| count | 假项目数量 | `number` | `1` |
| loading | 显示真实现 DOM | `boolean` | `false` |
| rows | 行数 | `number` | `3` |
| throttle | 渲染延迟(毫秒) | `number \| object` | `0` |

### Skeleton 插槽

| Name | Description | Type |
|------|-------------|------|
| default | 真实现渲染 DOM | `$attrs` |
| template | 骨架屏模| `{ key: number }` |

### SkeletonItem 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| variant | 骨架屏类型| `'p' \| 'text' \| 'h1' \| 'h3' \| 'caption' \| 'button' \| 'image' \| 'circle' \| 'rect'` | `'text'` |

## 最佳实践
1. 使用 `throttle` 防止快速加载时闪2. 使骨架屏结构与真实现内容匹3. 使用 `animated` 以获得更好用户体验
