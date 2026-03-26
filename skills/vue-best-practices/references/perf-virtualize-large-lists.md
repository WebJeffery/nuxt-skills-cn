---
title: 虚拟化大型列表
impact: HIGH
impactDescription: 大型列表(1000+ 项目)会显著降低性能;虚拟滚动是标准解决方案
type: best-practice
tags: [vue3, performance, lists, virtual-scroll, optimization]
---

# 虚拟化大型列表

**影响: HIGH** - 大型列表(1000+ 项目)会显著降低性能,因为每个项目都需要渲染和跟踪。虚拟滚动是标准解决方案。

## 任务列表

- 对大型列表(1000+ 项目)使用虚拟滚动
- 选择合适的虚拟滚动库
- 为列表项提供固定或可预测的高度
- 处理动态高度列表
- 优化列表项渲染性能

## 使用虚拟滚动库

**推荐库:**
- `vue-virtual-scroller`
- `vue-virtual-list`
- `vue3-virtual-scroll-list`

**正确示例:**
```vue
<template>
  <RecycleScroller
    class="scroller"
    :items="items"
    :item-size="50"
    key-field="id"
    v-slot="{ item }"
  >
    <div class="item">
      {{ item.name }}
    </div>
  </RecycleScroller>
</template>

<script setup>
import { RecycleScroller } from 'vue-virtual-scroller'
import 'vue-virtual-scroller/dist/vue-virtual-scroller.css'

const items = ref([...])
</script>
```

## 错误示例

```vue
<template>
  <div>
    <div v-for="item in items" :key="item.id" class="item">
      {{ item.name }}
    </div>
  </div>
</template>

<script setup>
const items = ref([...]) // 10000+ 项目
</script>
```

## 性能影响

- 渲染 1000+ 项目会导致明显的卡顿
- DOM 节点数量过多会影响浏览器性能
- 响应式跟踪开销随列表大小线性增长
- 滚动性能会显著下降

## 最佳实践

1. **何时使用虚拟滚动:**
   - 列表超过 1000 个项目
   - 列表项渲染成本较高
   - 需要流畅的滚动体验

2. **列表项高度:**
   - 固定高度性能最佳
   - 可变高度需要额外计算
   - 提供预估高度以提高性能

3. **优化列表项:**
   - 保持列表项简单
   - 避免在列表项中使用复杂组件
   - 使用 `v-once` 优化静态内容

4. **内存管理:**
   - 虚拟滚动会复用 DOM 节点
   - 注意清理事件监听器
   - 避免在列表项中存储大量状态

## 替代方案

对于中小型列表,考虑:
- 分页
- 无限滚动
- 延迟加载
