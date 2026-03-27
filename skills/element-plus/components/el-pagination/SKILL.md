---
name: "el-pagination"
description: "用于将大数据集分页分页组件。当用户需要显示分页数据、浏览页面控制页面大小和当前页时调用---

# 分页组件

分页组件将大量数据分成多个页以提供更好用户体验和性能
## 何时使用

- 以可管理块显示大数据集
- 浏览搜索结果
- 控制页面大小和当前页
- 显示总项目数和页面信
## 基本用法

```vue
<template>
  <el-pagination
    v-model:current-page="currentPage"
    v-model:page-size="pageSize"
    :page-sizes="[10, 20, 30, 40]"
    :total="total"
    layout="total, sizes, prev, pager, next, jumper"
    @size-change="handleSizeChange"
    @current-change="handleCurrentChange"
  />
</template>

<script setup>
import { ref } from 'vue'

const currentPage = ref(1)
const pageSize = ref(10)
const total = ref(100)

const handleSizeChange = (size) => {
  console.log('Page size:', size)
}

const handleCurrentChange = (page) => {
  console.log('Current page:', page)
}
</script>
```

## 布局配置

```vue
<template>
  <el-pagination
    :total="100"
    layout="prev, pager, next"
  />
  
  <el-pagination
    :total="100"
    layout="total, prev, pager, next"
  />
  
  <el-pagination
    :total="100"
    layout="sizes, prev, pager, next"
    :page-sizes="[10, 20, 30, 40]"
  />
  
  <el-pagination
    :total="100"
    layout="prev, pager, next, jumper"
  />
  
  <el-pagination
    :total="100"
    layout="total, sizes, prev, pager, next, jumper, ->, slot"
  >
    <span>Custom content</span>
  </el-pagination>
</template>
```

## 背景
```vue
<template>
  <el-pagination
    :total="100"
    background
    layout="prev, pager, next"
  />
</template>
```

## 小尺寸
```vue
<template>
  <el-pagination
    :total="100"
    size="small"
    layout="prev, pager, next"
  />
</template>
```

## 页码数量

```vue
<template>
  <el-pagination
    :total="1000"
    :pager-count="11"
    layout="prev, pager, next"
  />
</template>
```

## 单页时隐
```vue
<template>
  <el-pagination
    :total="10"
    :page-size="10"
    hide-on-single-page
    layout="prev, pager, next"
  />
</template>
```

## 自定义图标和文本

```vue
<template>
  <el-pagination
    :total="100"
    prev-text="Previous"
    next-text="Next"
    layout="prev, pager, next"
  />
  
  <el-pagination
    :total="100"
    :prev-icon="ArrowLeft"
    :next-icon="ArrowRight"
    layout="prev, pager, next"
  />
</template>

<script setup>
import { ArrowLeft, ArrowRight } from '@element-plus/icons-vue'
</script>
```

## 完整示例与数据获
```vue
<template>
  <el-table :data="tableData">
    <el-table-column prop="id" label="ID" />
    <el-table-column prop="name" label="Name" />
  </el-table>
  
  <el-pagination
    v-model:current-page="currentPage"
    v-model:page-size="pageSize"
    :page-sizes="[10, 20, 50, 100]"
    :total="total"
    layout="total, sizes, prev, pager, next, jumper"
    background
    @change="fetchData"
  />
</template>

<script setup>
import { ref, onMounted } from 'vue'

const tableData = ref([])
const currentPage = ref(1)
const pageSize = ref(10)
const total = ref(0)

const fetchData = async () => {
  const response = await fetch(
    `/api/data?page=${currentPage.value}&size=${pageSize.value}`
  )
  const data = await response.json()
  tableData.value = data.items
  total.value = data.total
}

onMounted(() => {
  fetchData()
})
</script>
```

## API 参考
### 属性
| Name | Description | Type | Default |
|------|-------------|------|---------|
| size | 分页尺寸 | `'large' \| 'default' \| 'small'` | `'default'` |
| background | 按钮是否有背景色 | `boolean` | `false` |
| page-size / v-model:page-size | 每页显示条目个数 | `number` | |
| default-page-size | 默认每页显示条目个数 | `number` | `10` |
| total | 总条目数 | `number` | |
| page-count | 总页| `number` | |
| pager-count | 显示页码数| `5 \| 7 \| 9 \| 11 \| 13 \| 15 \| 17 \| 19 \| 21` | `7` |
| current-page / v-model:current-page | 当前页数 | `number` | |
| default-current-page | 默认当前页数 | `number` | `1` |
| layout | 布局元素 | `string` | `'prev, pager, next, jumper, ->, total'` |
| page-sizes | 每页显示个数选择器选项设置 | `number[]` | `[10, 20, 30, 40, 50, 100]` |
| append-size-to | 每页显示个数选择器附加元素 | `string` | |
| popper-class | 每页显示个数选择器下拉框类型| `string` | `''` |
| popper-style | 每页显示个数选择器下拉框样式| `string \| object` | |
| prev-text | 上一页按钮文| `string` | `''` |
| prev-icon | 上一页按钮图| `string \| Component` | `ArrowLeft` |
| next-text | 下一页按钮文| `string` | `''` |
| next-icon | 下一页按钮图| `string \| Component` | `ArrowRight` |
| disabled | 禁用分页 | `boolean` | `false` |
| teleported | 是否将下拉菜单传送到 body | `boolean` | `true` |
| hide-on-single-page | 只有一页时是否隐藏 | `boolean` | `false` |

### 布局元素

| Element | Description |
|---------|-------------|
| `sizes` | 每页显示个数选择|
| `prev` | 上一页按钮|
| `pager` | 页码列表 |
| `next` | 下一页按钮|
| `jumper` | 跳转到某页输入框 |
| `->` | 此元素之后元素拉到右侧 |
| `total` | 总条目数显示 |
| `slot` | 自定义插槽内容|

### 事件

| Name | Description | Type |
|------|-------------|------|
| size-change | 每页显示条目个数改变时触| `(value: number) => void` |
| current-change | 当前页数改变时触| `(value: number) => void` |
| change | 页码每页条目数改变时触| `(currentPage: number, pageSize: number) => void` |
| prev-click | 点击上一页按钮时触发 | `(value: number) => void` |
| next-click | 点击下一页按钮时触发 | `(value: number) => void` |

### 插槽

| Name | Description |
|------|-------------|
| default | 自定义内容需要在 layout 中包`slot`) |

## 常见模式

### 使用 v-model 绑定

```vue
<template>
  <el-pagination
    v-model:current-page="page"
    v-model:page-size="size"
    :total="total"
    layout="total, sizes, prev, pager, next"
  />
</template>

<script setup>
import { ref, watch } from 'vue'

const page = ref(1)
const size = ref(10)
const total = ref(100)

watch([page, size], ([newPage, newSize]) => {
  console.log('Page:', newPage, 'Size:', newSize)
})
</script>
```

### 响应式分
```vue
<template>
  <el-pagination
    :total="total"
    :layout="isMobile ? 'prev, pager, next' : 'total, sizes, prev, pager, next, jumper'"
    :size="isMobile ? 'small' : 'default'"
  />
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const isMobile = ref(false)
const total = ref(100)

const checkMobile = () => {
  isMobile.value = window.innerWidth < 768
}

onMounted(() => {
  checkMobile()
  window.addEventListener('resize', checkMobile)
})

onUnmounted(() => {
  window.removeEventListener('resize', checkMobile)
})
</script>
```

### 自定义插槽内容
```vue
<template>
  <el-pagination
    :total="100"
    layout="slot, prev, pager, next"
  >
    <span class="pagination-info">Page {{ currentPage }}</span>
  </el-pagination>
</template>

<script setup>
import { ref } from 'vue'

const currentPage = ref(1)
</script>
```

## 组件交互

- **Table** 配合用于分页数据显示
- **Select** 结合使用自定义页面大小选项
- **Input** 结合使用自定义页面跳- **Card** 集成用于分页卡片列表

## 最佳实践
1. 使用 `v-model:current-page` `v-model:page-size` 进行双向绑定
2. 当总项目数可能较少时设`hide-on-single-page`
3. 在浅色背景上使用 `background` 以获得更好可见4. 根据可用宽度选择适当`pager-count`
5. 对紧凑布局使用 `size="small"`
6. 始终定义 `total` `page-count` 之一

## 弃用说明

- `small` 属性已弃用,请使`size="small"` 代替
- 事件仍然受支但推荐使`v-model` 绑定
