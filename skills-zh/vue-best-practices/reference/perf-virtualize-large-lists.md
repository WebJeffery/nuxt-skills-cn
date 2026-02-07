---
title: 虚拟化大型列表以避免 DOM 过载
impact: HIGH
impactDescription: 渲染数千个列表项会创建过多的 DOM 节点，导致渲染缓慢和内存使用率高
type: efficiency
tags: [vue3, performance, virtual-list, large-data, dom, optimization]
---

# 虚拟化大型列表以避免 DOM 过载

**影响: 高** - 渲染大型列表中的所有项目（数百或数千个）会创建大量的 DOM 节点。每个节点都消耗内存，减慢初始渲染，并使更新变得昂贵。列表虚拟化仅渲染可见项目，显著提高性能。

在处理可能超过 50-100 个项目的列表时使用虚拟化库，特别是如果项目具有复杂内容。

## 任务清单

- [ ] 识别渲染超过 50-100 个项目的列表
- [ ] 安装虚拟化库（vue-virtual-scroller、@tanstack/vue-virtual）
- [ ] 用虚拟化组件替换标准 `v-for`
- [ ] 确保列表项具有一致或可估计的高度
- [ ] 在开发期间使用真实的数据量进行测试

## 推荐的库

| 库 | 最适合 | 注意事项 |
|---------|----------|-------|
| `vue-virtual-scroller` | 通用，易于设置 | 最受欢迎，良好的默认值 |
| `@tanstack/vue-virtual` | 复杂布局，无头 | 框架无关，灵活 |
| `vue-virtual-scroll-grid` | 网格布局 | 2D 虚拟化 |
| `vueuc/VVirtualList` | Naive UI 项目 | Naive UI 生态系统的一部分 |

**错误：**
```vue
<template>
  <!-- 错误：立即渲染所有 10,000 个项目 -->
  <div class="user-list">
    <UserCard
      v-for="user in users"
      :key="user.id"
      :user="user"
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import UserCard from './UserCard.vue'

const users = ref([])

onMounted(async () => {
  // 创建 10,000 个 DOM 节点，浏览器挣扎
  users.value = await fetchAllUsers()
})
</script>
```

**正确：**
```vue
<template>
  <!-- 正确：一次只渲染约 20 个可见项目 -->
  <RecycleScroller
    class="user-list"
    :items="users"
    :item-size="80"
    key-field="id"
    v-slot="{ item }"
  >
    <UserCard :user="item" />
  </RecycleScroller>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { RecycleScroller } from 'vue-virtual-scroller'
import 'vue-virtual-scroller/dist/vue-virtual-scroller.css'
import UserCard from './UserCard.vue'

const users = ref([])

onMounted(async () => {
  // 内存中有 10,000 个项目，但只有约 20 个 DOM 节点
  users.value = await fetchAllUsers()
})
</script>

<style scoped>
.user-list {
  height: 600px; /* 容器必须有固定高度 */
}
</style>
```

## 使用 @tanstack/vue-virtual

```vue
<template>
  <div ref="parentRef" class="list-container">
    <div
      :style="{
        height: `${rowVirtualizer.getTotalSize()}px`,
        position: 'relative'
      }"
    >
      <div
        v-for="virtualRow in rowVirtualizer.getVirtualItems()"
        :key="virtualRow.key"
        :style="{
          position: 'absolute',
          top: 0,
          left: 0,
          width: '100%',
          height: `${virtualRow.size}px`,
          transform: `translateY(${virtualRow.start}px)`
        }"
      >
        <UserCard :user="users[virtualRow.index]" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useVirtualizer } from '@tanstack/vue-virtual'

const users = ref([/* 10,000 个用户 */])
const parentRef = ref(null)

const rowVirtualizer = useVirtualizer({
  count: users.value.length,
  getScrollElement: () => parentRef.value,
  estimateSize: () => 80,  // 估计的行高度
  overscan: 5  // 在视口上方/下方渲染 5 个额外项目
})
</script>

<style scoped>
.list-container {
  height: 600px;
  overflow: auto;
}
</style>
```

## 使用 vue-virtual-scroller 的动态高度

```vue
<template>
  <!-- 对于可变高度项目，使用 DynamicScroller -->
  <DynamicScroller
    :items="messages"
    :min-item-size="54"
    key-field="id"
  >
    <template #default="{ item, index, active }">
      <DynamicScrollerItem
        :item="item"
        :active="active"
        :data-index="index"
      >
        <ChatMessage :message="item" />
      </DynamicScrollerItem>
    </template>
  </DynamicScroller>
</template>

<script setup>
import { DynamicScroller, DynamicScrollerItem } from 'vue-virtual-scroller'
</script>
```

## 性能比较

| 方法 | 100 个项目 | 1,000 个项目 | 10,000 个项目 |
|----------|-----------|-------------|--------------|
| 常规 v-for | ~100 个 DOM 节点 | ~1,000 个 DOM 节点 | ~10,000 个 DOM 节点 |
| 虚拟化 | ~20 个 DOM 节点 | ~20 个 DOM 节点 | ~20 个 DOM 节点 |
| 初始渲染 | 快 | 慢 | 很慢 / 崩溃 |
| 虚拟化渲染 | 快 | 快 | 快 |

## 何时不虚拟化

- 具有简单内容的 50 个项目以下的列表
- 屏幕阅读器必须同时访问所有项目的列表
- 必须渲染所有内容的打印布局
- 必须在初始 HTML 中的 SEO 关键内容

## 参考
- [Vue.js 性能 - 虚拟化大型列表](https://vuejs.org/guide/best-practices/performance.html#virtualize-large-lists)
- [vue-virtual-scroller 文档](https://github.com/Akryum/vue-virtual-scroller)
- [TanStack Virtual](https://tanstack.com/virtual/latest)
