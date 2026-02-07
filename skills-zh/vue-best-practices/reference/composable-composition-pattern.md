---
title: 组合 Composables 以实现复杂逻辑
impact: MEDIUM
impactDescription: 从其他 composables 构建 composables 创建可重用、可测试的构建块
type: best-practice
tags: [vue3, composables, composition-api, patterns, code-organization]
---

# 组合 Composables 以实现复杂逻辑

**影响: 中等** - Composables 可以（并且应该）调用其他 composables。这种组合模式允许您从更小、专注、可重用的部分构建复杂功能。每个 composable 处理一个关注点，更高级别的 composables 组合它们。

这是 Composition API 相对于 mixins 的关键优势之一 - 依赖关系是显式和可追踪的。

## 任务清单

- [ ] 将可重用逻辑提取到专注的、单一用途的 composables 中
- [ ] 通过组合更简单的 composables 来构建复杂的 composables
- [ ] 确保每个 composable 具有单一职责
- [ ] 通过参数或 refs 在组合的 composables 之间传递数据

**示例: 从更小的 Composables 构建鼠标跟踪器**

```javascript
// composables/useEventListener.js - 低级构建块
import { onMounted, onUnmounted, toValue } from 'vue'

export function useEventListener(target, event, callback) {
  onMounted(() => {
    const el = toValue(target)
    el.addEventListener(event, callback)
  })

  onUnmounted(() => {
    const el = toValue(target)
    el.removeEventListener(event, callback)
  })
}

// composables/useMouse.js - 组合 useEventListener
import { ref } from 'vue'
import { useEventListener } from './useEventListener'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  // 重用事件监听器 composable
  useEventListener(window, 'mousemove', update)

  return { x, y }
}

// composables/useMouseInElement.js - 组合 useMouse
import { ref, computed } from 'vue'
import { useMouse } from './useMouse'

export function useMouseInElement(elementRef) {
  const { x, y } = useMouse()

  const elementX = computed(() => {
    if (!elementRef.value) return 0
    const rect = elementRef.value.getBoundingClientRect()
    return x.value - rect.left
  })

  const elementY = computed(() => {
    if (!elementRef.value) return 0
    const rect = elementRef.value.getBoundingClientRect()
    return y.value - rect.top
  })

  const isOutside = computed(() => {
    if (!elementRef.value) return true
    const rect = elementRef.value.getBoundingClientRect()
    return x.value < rect.left || x.value > rect.right ||
           y.value < rect.top || y.value > rect.bottom
  })

  return { x, y, elementX, elementY, isOutside }
}
```

## 模式: Composable 依赖链

```javascript
// 第 1 层: 原语
export function useEventListener(target, event, callback) { /* ... */ }
export function useInterval(callback, delay) { /* ... */ }
export function useTimeout(callback, delay) { /* ... */ }

// 第 2 层: 基于原语构建
export function useWindowSize() {
  const width = ref(window.innerWidth)
  const height = ref(window.innerHeight)

  useEventListener(window, 'resize', () => {
    width.value = window.innerWidth
    height.value = window.innerHeight
  })

  return { width, height }
}

export function useOnline() {
  const isOnline = ref(navigator.onLine)

  useEventListener(window, 'online', () => isOnline.value = true)
  useEventListener(window, 'offline', () => isOnline.value = false)

  return { isOnline }
}

// 第 3 层: 组合多个 composables 的复杂功能
export function useAutoSave(dataRef, saveFunction, options = {}) {
  const { debounce = 1000, onlyWhenOnline = true } = options

  const { isOnline } = useOnline()
  const isSaving = ref(false)
  const lastSaved = ref(null)

  let timeoutId = null

  watch(dataRef, (newData) => {
    if (onlyWhenOnline && !isOnline.value) return

    clearTimeout(timeoutId)
    timeoutId = setTimeout(async () => {
      isSaving.value = true
      try {
        await saveFunction(newData)
        lastSaved.value = new Date()
      } finally {
        isSaving.value = false
      }
    }, debounce)
  }, { deep: true })

  return { isSaving, lastSaved, isOnline }
}
```

## 模式: 使用组合进行代码组织

当组件变得复杂时提取内联 composables：

```vue
<script setup>
// 之前: 所有逻辑混合在一起
import { ref, computed, watch, onMounted } from 'vue'

const searchQuery = ref('')
const filters = ref({ category: null, minPrice: 0 })
const products = ref([])
const isLoading = ref(false)
const error = ref(null)
const sortBy = ref('name')
const sortOrder = ref('asc')

// ...50 多行混合关注点的代码
</script>
```

```vue
<script setup>
// 之后: 分离为专注的 composables
import { useProductSearch } from './composables/useProductSearch'
import { useProductFilters } from './composables/useProductFilters'
import { useProductSort } from './composables/useProductSort'

const { searchQuery, debouncedQuery } = useProductSearch()
const { filters, activeFilters, clearFilters } = useProductFilters()
const { sortBy, sortOrder, sortedProducts } = useProductSort()

// 每个 composable 都是专注的、可测试的，并且可能是可重用的
</script>
```

## 在组合的 Composables 之间传递数据

```javascript
// Composables 可以接受来自其他 composables 的 refs
export function useFilteredProducts(products, filters) {
  return computed(() => {
    let result = toValue(products)

    if (filters.value.category) {
      result = result.filter(p => p.category === filters.value.category)
    }

    if (filters.value.minPrice > 0) {
      result = result.filter(p => p.price >= filters.value.minPrice)
    }

    return result
  })
}

export function useSortedProducts(products, sortConfig) {
  return computed(() => {
    const items = [...toValue(products)]
    const { field, order } = sortConfig.value

    return items.sort((a, b) => {
      const comparison = a[field] > b[field] ? 1 : -1
      return order === 'asc' ? comparison : -comparison
    })
  })
}

// 用法 - composables 通过它们的输出链接
const { products, isLoading } = useFetch('/api/products')
const { filters } = useFilters()
const filteredProducts = useFilteredProducts(products, filters)
const { sortConfig } = useSortConfig()
const sortedProducts = useSortedProducts(filteredProducts, sortConfig)
```

## 相对于 Mixins 的优势

| Composables | Mixins |
|-------------|--------|
| 通过导入显式依赖 | 隐式依赖 |
| 通过参数清晰的数据流 | 不清楚哪个 mixin 提供什么 |
| 没有命名空间冲突 | 属性可能冲突 |
| 易于跟踪和调试 | 难以跟踪来源 |
| TypeScript 友好 | TypeScript 支持差 |

## 参考
- [Vue.js Composables](https://vuejs.org/guide/reusability/composables.html)
- [Vue.js Composables vs Mixins](https://vuejs.org/guide/reusability/composables.html#comparisons-with-other-techniques)
