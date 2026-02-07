---
title: 数组和集合中的 Refs 需要 .value
impact: MEDIUM
impactDescription: 响应式数组、Map 或 Set 中的 refs 不会像在响应式对象中那样自动解包
type: capability
tags: [vue3, reactivity, ref, arrays, collections, unwrapping]
---

# 数组和集合中的 Refs 需要 .value

**影响：中** - 与 ref 作为响应式对象的属性时不同，响应式数组、Map 和 Set 中的 refs 不会自动解包。你必须使用 `.value` 访问它们，忘记这一点会导致静默错误。

Vue 只有当 refs 是响应式对象的属性时才会自动解包 refs。当 refs 是数组中的元素或 Map/Set 中的值时，它们保持为 ref 对象，需要显式的 `.value` 访问。

## 任务清单

- [ ] 访问存储在响应式数组中的 refs 时始终使用 `.value`
- [ ] 访问存储在响应式 Map 或 Set 中的 refs 时始终使用 `.value`
- [ ] 考虑在集合中存储普通值而不是 refs 以避免混淆
- [ ] 遍历包含 refs 的数组时要注意这一点

**错误：**
```javascript
import { ref, reactive } from 'vue'

const books = reactive([ref('Vue 3 Guide')])
const counts = reactive(new Map([['clicks', ref(0)]]))

// 错误：数组中的 refs 不会解包
console.log(books[0])        // Ref 对象，不是 'Vue 3 Guide'
books[0] = 'New Title'       // 替换 ref，不更新它！

// 错误：Map 中的 refs 不会解包
console.log(counts.get('clicks'))     // Ref 对象，不是 0
counts.get('clicks')++                // 没有任何作用
```

**正确：**
```javascript
import { ref, reactive } from 'vue'

const books = reactive([ref('Vue 3 Guide')])
const counts = reactive(new Map([['clicks', ref(0)]]))

// 正确：对数组中的 refs 使用 .value
console.log(books[0].value)    // 'Vue 3 Guide'
books[0].value = 'New Title'   // 更新 ref 的值

// 正确：对 Map 中的 refs 使用 .value
console.log(counts.get('clicks').value)  // 0
counts.get('clicks').value++             // 递增到 1
```

```javascript
// 替代方案：只在集合中存储普通值（更简单）
const books = reactive(['Vue 3 Guide', 'Vuex Handbook'])
const counts = reactive(new Map([['clicks', 0]]))

// 不需要 .value - 但对单个项目的更改不会独立响应
console.log(books[0])            // 'Vue 3 Guide'
console.log(counts.get('clicks')) // 0

// 变异仍然通过响应式包装器触发响应性
books[0] = 'New Title'           // 有效
counts.set('clicks', counts.get('clicks') + 1)  // 有效
```

```vue
<template>
  <!-- 在模板中，数组中的 refs 也需要特殊处理 -->
  <div v-for="(book, index) in books" :key="index">
    <!-- 如果 book 是 ref，你需要： -->
    {{ book.value }}

    <!-- 或者使用 computed 先解包它们 -->
  </div>
</template>
```

## 参考
- [Vue.js 响应式基础 - 数组和集合中的注意事项](https://vuejs.org/guide/essentials/reactivity-fundamentals.html#caveat-in-arrays-and-collections)
