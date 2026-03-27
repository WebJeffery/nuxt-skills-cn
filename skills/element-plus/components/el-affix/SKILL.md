---
name: "el-affix"
description: "用于将元素固定到特定可见区域的固定组件。当用户需要创建粘性导航、固定页眉或滚动时保持可见元素时调用
---

# Affix 组件

Affix 组件将元素固定到特定可见区域,适用于粘性导航、页眉工具栏
## 何时使用

- 粘性导航页- 滚动时固定工具栏
- 持续操作按钮
- 浮动侧边
## 基本用法

```vue
<template>
  <el-affix :offset="120">
    <el-button type="primary">Offset top 120px</el-button>
  </el-affix>
</template>
```

## 目标容器

```vue
<template>
  <div class="scroll-container" style="height: 400px; overflow: auto;">
    <el-affix target=".scroll-container" :offset="80">
      <el-button type="primary">Fixed in container</el-button>
    </el-affix>
  </div>
</template>
```

## 固定位置

```vue
<template>
  <el-affix position="top" :offset="20">
    <el-button type="primary">Fixed at top</el-button>
  </el-affix>
  
  <el-affix position="bottom" :offset="20">
    <el-button type="primary">Fixed at bottom</el-button>
  </el-affix>
</template>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| offset | 偏移距离 | `number` | `0` |
| position | 固定位置 | `'top' \| 'bottom'` | `'top'` |
| target | 目标容器(CSS 选择 | `string` | |
| z-index | 固定z-index | `number` | `100` |
| teleported | 固定元素是否传| `boolean` | `false` |
| append-to | 固定附加到哪个元| `CSSSelector \| HTMLElement` | `'body'` |

### 事件

| Name | Description | Type |
|------|-------------|------|
| change | 固定状态改变时触发 | `(fixed: boolean) => void` |
| scroll | 滚动时触| `({ scrollTop: number, fixed: boolean }) => void` |

### 插槽

| Name | Description |
|------|-------------|
| default | 自定义默认内容|

### 暴露

| Name | Description | Type |
|------|-------------|------|
| update | 手动更新固定状态| `() => void` |
| updateRoot | 更新 rootRect 信息 | `() => void` |

## 常见模式

### 带状态跟踪粘性页
```vue
<template>
  <el-affix :offset="0" @change="handleAffixChange">
    <header :class="{ 'is-fixed': isFixed }">
      <nav>Sticky Navigation</nav>
    </header>
  </el-affix>
</template>

<script setup>
import { ref } from 'vue'

const isFixed = ref(false)

const handleAffixChange = (fixed) => {
  isFixed.value = fixed
}
</script>
```

## 最佳实践
1. 使用 `target` 将固定限制到特定容器
2. 设置适当`z-index` 以避免分层问3. 处理 `change` 事件用于 UI 状态更