---
title: 监视响应式对象属性时使用 getter 函数
impact: HIGH
impactDescription: 直接监视响应式对象的属性传递原始值，导致监视器永远不会触发
type: capability
tags: [vue3, watch, watchers, reactive, getter, common-mistake]
---

# 监视响应式对象属性时使用 getter 函数

**影响：高** - 直接监视响应式对象的属性会将原始值传递给 `watch()`，而不是响应式引用。监视器永远不会触发，因为原始值不是响应式的。

当你需要监视响应式对象的特定属性时，始终将其包装在 getter 函数 `() => obj.property` 中。

## 任务清单

- [ ] 监视响应式对象的属性时始终使用 getter 函数
- [ ] 记住 `watch(obj.count, ...)` 传递当前值，而不是响应式引用
- [ ] 对于 ref，你可以直接监视：`watch(myRef, ...)`
- [ ] 对于整个响应式对象，你可以直接监视（创建隐式深度监视器）

**错误：**
```javascript
import { reactive, watch } from 'vue'

const state = reactive({ count: 0, name: 'Vue' })

// 错误：将数字 0 传递给 watch()，而不是响应式引用
// 此监视器永远不会触发！
watch(state.count, (newCount) => {
  console.log(`Count 更改为：${newCount}`)
})

// 错误：字符串属性也有同样的问题
watch(state.name, (newName) => {
  console.log(`Name 更改为：${newName}`)
})
```

**正确：**
```javascript
import { reactive, watch } from 'vue'

const state = reactive({ count: 0, name: 'Vue' })

// 正确：使用 getter 函数
watch(
  () => state.count,
  (newCount, oldCount) => {
    console.log(`Count 从 ${oldCount} 更改为 ${newCount}`)
  }
)

// 正确：使用 getter 监视多个属性
watch(
  () => state.name,
  (newName) => {
    console.log(`Name 更改为：${newName}`)
  }
)

// 正确：监视派生值
watch(
  () => state.count * 2,
  (doubledCount) => {
    console.log(`双倍计数：${doubledCount}`)
  }
)
```

## 监视多个属性

```javascript
import { reactive, watch } from 'vue'

const state = reactive({ firstName: 'John', lastName: 'Doe' })

// 使用 getter 数组监视多个属性
watch(
  [() => state.firstName, () => state.lastName],
  ([newFirst, newLast], [oldFirst, oldLast]) => {
    console.log(`Name 从 ${oldFirst} ${oldLast} 更改为 ${newFirst} ${newLast}`)
  }
)
```

## 何时直接监视有效

```javascript
import { ref, reactive, watch } from 'vue'

const count = ref(0)
const state = reactive({ nested: { value: 1 } })

// 正确：ref 可以直接监视
watch(count, (newVal) => {
  console.log(`Count: ${newVal}`)
})

// 正确：整个响应式对象创建隐式深度监视器
watch(state, (newState) => {
  // 在任何嵌套更改时触发
  // 注意：newState === oldState（相同的对象引用）
})
```

## 参考
- [Vue.js 监听器 - 监视源类型](https://vuejs.org/guide/essentials/watchers.html#watch-source-types)
