---
name: "el-infinite-scroll"
description: "用于滚动到底部时加载更多数据无限滚动指令。当用户需要实现无限滚动、延迟加载滚动分页时调用---

# InfiniteScroll 指令

InfiniteScroll 在到达页面底部时加载更多数据
## 何时使用

- 无限滚动列表
- 延迟加载内容
- 滚动分页
- 信息流布局

## 基本用法

```vue
<template>
  <ul v-infinite-scroll="load" class="infinite-list">
    <li v-for="i in count" :key="i" class="infinite-list-item">
      {{ i }}
    </li>
  </ul>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)

const load = () => {
  count.value += 2
}
</script>
```

## 禁用加载

```vue
<template>
  <ul
    v-infinite-scroll="load"
    :infinite-scroll-disabled="disabled"
    class="infinite-list"
  >
    <li v-for="i in count" :key="i">{{ i }}</li>
  </ul>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)
const disabled = ref(false)

const load = () => {
  if (count.value >= 20) {
    disabled.value = true
    return
  }
  count.value += 2
}
</script>
```

## API 参考
### 指令选项

| Name | Description | Type | Default |
|------|-------------|------|---------|
| v-infinite-scroll | 加载方法 | `Function` | |
| infinite-scroll-disabled | 禁用加载 | `boolean` | `false` |
| infinite-scroll-delay | 节流延迟(毫秒) | `number` | `200` |
| infinite-scroll-distance | 触发距离(像素) | `number` | `0` |
| infinite-scroll-immediate | 立即执行 | `boolean` | `true` |

## 最佳实践
1. 使用 `infinite-scroll-disabled` 在没有更多数据时停止加载
2. 设置适当`infinite-scroll-distance` 以获得更好用户体验
3. Scrollbar 组件一起使用以获得更好性能
