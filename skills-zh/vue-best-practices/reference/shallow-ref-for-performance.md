---
title: 对大型对象和外部数据使用 shallowRef
impact: MEDIUM
impactDescription: 对大型对象的深度响应性会导致性能开销 - shallowRef 只跟踪 .value 更改
type: efficiency
tags: [vue3, reactivity, shallowRef, performance, optimization]
---

# 对大型对象和外部数据使用 shallowRef

**影响：中** - 默认情况下，`ref()` 使对象深度响应式，将所有嵌套属性包装在 Proxies 中。对于大型数据结构、外部库或不可变数据，这会导致不必要的开销。使用 `shallowRef()` 只跟踪 `.value` 更改。

`shallowRef()` 非常适合来自 API 的大型数据集、类实例、DOM 节点或由外部库管理的对象。Vue 只跟踪 `.value` 被替换的时间，而不是内部变异，显著减少响应性开销。

## 任务清单

- [ ] 对不会变异的大型 API 响应数据使用 `shallowRef()`
- [ ] 对外部库对象（地图、图表等）使用 `shallowRef()`
- [ ] 对具有自己状态管理的类实例使用 `shallowRef()`
- [ ] 对永远不应该是响应式的对象使用 `markRaw()`（例如，第三方实例）
- [ ] 记住：使用 shallowRef 时，必须完全替换 `.value` 才能触发更新

**错误：**
```javascript
import { ref } from 'vue'

// 低效：对大型数据集的深度响应性
const users = ref(await fetchUsers()) // 10,000 个用户，每个都深度响应式

// 低效：包装在 Proxy 中的外部库
const mapInstance = ref(new mapboxgl.Map({ /* ... */ }))

// 低效：大型不可变 API 响应
const apiResponse = ref(await fetch('/api/big-data').then(r => r.json()))
```

**正确：**
```javascript
import { shallowRef, markRaw, triggerRef } from 'vue'

// 高效：只跟踪 .value 替换
const users = shallowRef(await fetchUsers())

// 通过替换整个数组来更新
users.value = await fetchUsers()

// 如果你变异并需要触发更新，使用 triggerRef
users.value.push(newUser)
triggerRef(users) // 手动触发监听器

// 高效：外部库对象
const mapInstance = shallowRef(null)
onMounted(() => {
  mapInstance.value = new mapboxgl.Map({ /* ... */ })
})

// 对永远不应该是响应式的对象最好
const thirdPartyLib = markRaw(new SomeLibrary())
// 即使在 reactive() 中使用，此对象也不会被包装在 Proxy 中
```

```vue
<script setup>
import { shallowRef } from 'vue'

// 大型分页数据 - 只关心页面何时更改
const pageData = shallowRef([])

async function loadPage(page) {
  // 完全替换以触发响应性
  pageData.value = await api.getPage(page)
}

// 模板仍然有效 - shallowRef 在模板中解包
</script>

<template>
  <div v-for="item in pageData" :key="item.id">
    {{ item.name }}
  </div>
</template>
```

```javascript
// 比较：ref() vs shallowRef()

// 使用 ref()：Vue 包装每个嵌套属性
const deep = ref({
  level1: {
    level2: {
      level3: { value: 1 }
    }
  }
})
deep.value.level1.level2.level3.value++ // 被跟踪！

// 使用 shallowRef()：只跟踪 .value
const shallow = shallowRef({
  level1: {
    level2: {
      level3: { value: 1 }
    }
  }
})
shallow.value.level1.level2.level3.value++ // 未被跟踪！
shallow.value = { /* 新对象 */ } // 被跟踪！
```

## 参考
- [Vue.js 响应式基础 - 减少响应性开销](https://vuejs.org/guide/best-practices/performance.html#reduce-reactivity-overhead-for-large-immutable-structures)
- [Vue.js shallowRef API](https://vuejs.org/api/reactivity-advanced.html#shallowref)
- [Vue.js markRaw API](https://vuejs.org/api/reactivity-advanced.html#markraw)
