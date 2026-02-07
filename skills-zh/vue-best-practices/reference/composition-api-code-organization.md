---
title: 按逻辑关注点组织组合式 API 代码,而不是按选项类型
impact: MEDIUM
impactDescription: 组合式 API 中的代码组织不当会导致比选项 API 更糟糕的意大利面条式代码
type: best-practice
tags: [vue3, composition-api, code-organization, refactoring, maintainability]
---

# 按逻辑关注点组织组合式 API 代码,而不是按选项类型

**影响:中等** - 组合式 API 删除了选项 API 的"护栏",这些护栏强制将代码放入 data/methods/computed 桶中。如果没有有意的组织,组合式 API 代码可能比选项 API 更混乱。按功能或逻辑关注点将相关代码分组在一起。

关键见解是组合式 API 提供了灵活性 - 这需要纪律性。应用您对任何结构良好的 JavaScript 代码使用的相同代码组织原则。

## 任务清单

- [ ] 按功能将相关的状态、计算和方法分组在一起
- [ ] 当相关逻辑增长时,将其提取到组合式函数中
- [ ] 不要在整个脚本部分分散相关代码
- [ ] 使用注释或区域在较大的组件中划分逻辑部分
- [ ] 考虑将大型组件拆分为较小的组件或组合式函数

**无组织(不好):**
```vue
<script setup>
// 分散的代码 - 很难理解什么与什么相关
import { ref, computed, onMounted, watch } from 'vue'

const searchQuery = ref('')
const items = ref([])
const selectedItem = ref(null)
const isModalOpen = ref(false)
const sortOrder = ref('asc')
const filterCategory = ref('all')
const isLoading = ref(false)
const error = ref(null)

const filteredItems = computed(() => {
  return items.value.filter(i => i.category === filterCategory.value)
})

function openModal() { isModalOpen.value = true }

const sortedItems = computed(() => {
  return [...filteredItems.value].sort(/* ... */)
})

function closeModal() { isModalOpen.value = false }

watch(searchQuery, async (query) => { /* fetch */ })

function selectItem(item) { selectedItem.value = item }

const searchResults = computed(() => {
  return items.value.filter(i => i.name.includes(searchQuery.value))
})

onMounted(() => { fetchItems() })

async function fetchItems() { /* ... */ }
</script>
```

**按关注点组织(好):**
```vue
<script setup>
import { ref, computed, onMounted, watch } from 'vue'

// ============================================
// 数据获取和状态
// ============================================
const items = ref([])
const isLoading = ref(false)
const error = ref(null)

async function fetchItems() {
  isLoading.value = true
  try {
    items.value = await api.getItems()
  } catch (e) {
    error.value = e
  } finally {
    isLoading.value = false
  }
}

onMounted(() => fetchItems())

// ============================================
// 搜索
// ============================================
const searchQuery = ref('')

const searchResults = computed(() =>
  items.value.filter(i =>
    i.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
)

watch(searchQuery, async (query) => {
  if (query.length > 2) {
    await fetchItems({ search: query })
  }
})

// ============================================
// 过滤和排序
// ============================================
const filterCategory = ref('all')
const sortOrder = ref('asc')

const filteredItems = computed(() =>
  searchResults.value.filter(i =>
    filterCategory.value === 'all' || i.category === filterCategory.value
  )
)

const sortedItems = computed(() =>
  [...filteredItems.value].sort((a, b) =>
    sortOrder.value === 'asc' ? a.name.localeCompare(b.name) : b.name.localeCompare(a.name)
  )
)

// ============================================
// 选择和模态框
// ============================================
const selectedItem = ref(null)
const isModalOpen = ref(false)

function selectItem(item) {
  selectedItem.value = item
  openModal()
}

function openModal() { isModalOpen.value = true }
function closeModal() {
  isModalOpen.value = false
  selectedItem.value = null
}
</script>
```

**最佳: 提取到组合式函数:**
```vue
<script setup>
import { useItems } from '@/composables/useItems'
import { useSearch } from '@/composables/useSearch'
import { useModal } from '@/composables/useModal'

// 每个组合式函数封装一个逻辑关注点
const { items, isLoading, error, fetchItems } = useItems()
const { searchQuery, searchResults } = useSearch(items)
const {
  selectedItem,
  isOpen: isModalOpen,
  open: openModal,
  close: closeModal
} = useModal()

function selectItem(item) {
  selectedItem.value = item
  openModal()
}
</script>

// composables/useItems.js
export function useItems() {
  const items = ref([])
  const isLoading = ref(false)
  const error = ref(null)

  async function fetchItems(params = {}) {
    isLoading.value = true
    try {
      items.value = await api.getItems(params)
    } catch (e) {
      error.value = e
    } finally {
      isLoading.value = false
    }
  }

  onMounted(() => fetchItems())

  return { items, isLoading, error, fetchItems }
}
```

## 组件需要重构的迹象

1. **在相关代码之间滚动** - 如果您跳来跳去以理解一个功能
2. **脚本设置中有 300+ 行** - 考虑提取组合式函数
3. **多个不相关的功能** - 每个都应该是自己的组合式函数
4. **重复的类似模式** - 提取到共享的组合式函数

## 何时提取到组合式函数

```javascript
// 提取当:
// - 逻辑在组件之间重用
// - 功能是自包含的(搜索、分页、表单处理)
// - 组件变得太大(>200 行)
// - 您想单独测试逻辑

// 保持内联当:
// - 逻辑简单且特定于组件
// - 提取会增加复杂性而不是减少复杂性
// - 组件已经很小且专注
```

## 参考
- [组合式 API FAQ - 更灵活的代码组织](https://vuejs.org/guide/extras/composition-api-faq.html#more-flexible-code-organization)
- [组合式函数](https://vuejs.org/guide/reusability/composables.html)
