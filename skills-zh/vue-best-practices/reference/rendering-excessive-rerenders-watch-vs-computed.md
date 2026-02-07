---
title: 避免因误用监视器导致的过度重新渲染
impact: HIGH
impactDescription: 使用 watch 而不是 computed，或不必要地使用深度监视器，会触发过度的组件重新渲染
type: gotcha
tags: [vue3, rendering, performance, watch, computed, reactivity, re-renders]
---

# 避免因误用监视器导致的过度重新渲染

**影响：高** - 不当使用监视器是性能问题的常见原因。深度监视器跟踪每个嵌套属性的变化，在 computed 足够时使用 watch 会创建不必要的响应式更新，从而触发重新渲染。

## 任务清单

- [ ] 使用 `computed` 计算派生值，而不是 `watch` + 手动状态更新
- [ ] 除非绝对必要，否则避免在大对象上使用 `deep: true`
- [ ] 当需要深度监视时，改为监视特定的嵌套路径
- [ ] 永远不要在 watch 内触发导致 watch 重新触发的状态变化

**错误：**
```vue
<script setup>
import { ref, watch } from 'vue'

const user = ref({ name: 'John', settings: { theme: 'dark', notifications: true } })
const displayName = ref('')

// 错误：使用 watch 计算派生值
// 这会触发额外的响应式更新周期
watch(() => user.value.name, (name) => {
  displayName.value = `用户: ${name}`
}, { immediate: true })

// 错误：在大对象上使用深度监视器
// 在任何嵌套更改时触发，即使是不相关的更改
const items = ref([/* 1000 个具有嵌套属性的项目 */])
watch(items, (newItems) => {
  console.log('项目已更改')  // 在每个微小更改时触发
}, { deep: true })
</script>
```

**正确：**
```vue
<script setup>
import { ref, computed, watch } from 'vue'

const user = ref({ name: 'John', settings: { theme: 'dark', notifications: true } })

// 正确：使用 computed 计算派生值
// 没有额外的响应式更新，自动缓存
const displayName = computed(() => `用户: ${user.value.name}`)

// 正确：监视特定路径，而不是整个对象
const items = ref([/* 1000 个项目 */])
watch(
  () => items.value.length,  // 仅监视长度
  (newLength) => {
    console.log(`项目计数: ${newLength}`)
  }
)

// 正确：监视特定嵌套属性
watch(
  () => user.value.settings.theme,
  (newTheme) => {
    applyTheme(newTheme)  // 副作用 - 适用于 watch
  }
)
</script>
```

## 何时使用 Watch 与 Computed

| 使用场景 | 使用此方法 |
|----------|----------|
| 从状态派生值 | `computed` |
| 格式化/转换数据以供显示 | `computed` |
| 执行副作用（API 调用、DOM 更新） | `watch` |
| 响应路由更改 | `watch` |
| 与外部系统同步 | `watch` |

## 来自 Watch 的无限循环

```vue
<script setup>
import { ref, watch } from 'vue'

const count = ref(0)

// 危险：无限循环！
watch(count, (newVal) => {
  count.value = newVal + 1  // 修改被监视的源 -> 再次触发 watch
})

// 正确：使用 computed 或避免自修改
const doubleCount = computed(() => count.value * 2)
</script>
```

## 高效的深度监视

当你必须监视复杂对象时：

```vue
<script setup>
import { ref, watch, toRaw } from 'vue'

const formData = ref({
  personal: { name: '', email: '' },
  address: { street: '', city: '' },
  preferences: { /* 许多属性 */ }
})

// 错误：监视所有内容，包括首选项更改
watch(formData, () => {
  saveForm()
}, { deep: true })

// 正确：仅监视你关心的部分
watch(
  () => formData.value.personal,
  () => savePersonalSection(),
  { deep: true }  // 仅在此小子树上深度
)

// 正确：监视序列化版本以进行更改检测
watch(
  () => JSON.stringify(formData.value),
  () => {
    markFormDirty()
  }
)
</script>
```

## 数组变异陷阱

```vue
<script setup>
import { ref, watch } from 'vue'

const items = ref([1, 2, 3])

// 没有 deep，此 watch 不会在排序/反转时触发！
watch(items, () => {
  console.log('项目已更改')
})

items.value.sort()  // Watch 不触发 - 数组引用未更改

// 解决方案 1：使用 deep（性能成本）
watch(items, callback, { deep: true })

// 解决方案 2：替换数组而不是变异
items.value = [...items.value].sort()
</script>
```

## 参考
- [Vue.js 监视器](https://vuejs.org/guide/essentials/watchers.html)
- [Vue.js 计算属性](https://vuejs.org/guide/essentials/computed.html)
- [Vue.js 性能 - 响应式](https://vuejs.org/guide/best-practices/performance.html#reduce-reactivity-overhead-for-large-immutable-structures)
