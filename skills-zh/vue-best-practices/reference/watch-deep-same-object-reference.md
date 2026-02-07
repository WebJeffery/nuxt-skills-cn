---
title: 深度监视回调为旧值和新值接收相同的对象引用
impact: MEDIUM
impactDescription: 在深度监视器中比较 oldValue 和 newValue 具有误导性，因为它们引用相同的对象
type: capability
tags: [vue3, watch, watchers, deep, oldValue, newValue, object-reference]
---

# 深度监视回调为旧值和新值接收相同的对象引用

**影响：中** - 在响应式对象上使用深度监视器时，回调中的 `newValue` 和 `oldValue` 都指向相同的对象引用。对于嵌套变异，它们将始终相等，因为 Vue 在变异之前不会克隆对象。

不要依赖在深度监视器中比较 `newValue` 和 `oldValue` 来检测更改。相反，跟踪特定值或实现你自己的差异比较。

## 任务清单

- [ ] 不要在深度监视器中比较 newValue === oldValue 来检测更改
- [ ] 对于更改检测，改为监视特定属性
- [ ] 如果你需要旧值，请在更改之前手动快照
- [ ] 考虑使用序列化方法进行复杂的差异比较需求
- [ ] 仅在替换整个对象时值才不同

**错误：**
```javascript
import { reactive, watch } from 'vue'

const state = reactive({
  user: {
    name: 'John',
    preferences: { theme: 'dark' }
  }
})

// 错误：尝试比较旧值和新值
watch(
  () => state.user,
  (newUser, oldUser) => {
    // 对于嵌套变异，此比较始终为真！
    if (newUser === oldUser) {
      console.log('相同的引用！')  // 对于嵌套更改始终记录
    }

    // 这也不会起作用 - 它们是同一个对象
    if (newUser.name !== oldUser.name) {
      console.log('Name 已更改')  // 对于嵌套变异从不记录
    }
  },
  { deep: true }
)

// 当发生这种情况时：
state.user.name = 'Jane'
// newUser 和 oldUser 都是 { name: 'Jane', preferences: { theme: 'dark' } }
```

**正确：**
```javascript
import { reactive, watch, ref } from 'vue'

const state = reactive({
  user: {
    name: 'John',
    preferences: { theme: 'dark' }
  }
})

// 正确：监视你关心的特定属性
watch(
  () => state.user.name,
  (newName, oldName) => {
    console.log(`Name 从 "${oldName}" 更改为 "${newName}"`)
    // oldName 和 newName 是原始值，可以正确工作
  }
)

// 正确：监视多个特定属性
watch(
  [() => state.user.name, () => state.user.preferences.theme],
  ([newName, newTheme], [oldName, oldTheme]) => {
    if (newName !== oldName) {
      console.log(`Name: ${oldName} -> ${newName}`)
    }
    if (newTheme !== oldTheme) {
      console.log(`Theme: ${oldTheme} -> ${newTheme}`)
    }
  }
)
```

## 手动快照模式

```javascript
import { reactive, watch, ref } from 'vue'

const state = reactive({ count: 0, items: [] })

// 保留手动快照以进行比较
const previousSnapshot = ref(JSON.stringify(state))

watch(
  state,
  (newState) => {
    const currentSnapshot = JSON.stringify(newState)

    if (currentSnapshot !== previousSnapshot.value) {
      const oldData = JSON.parse(previousSnapshot.value)
      console.log('旧：', oldData)
      console.log('新：', newState)

      // 更新快照以进行下一次比较
      previousSnapshot.value = currentSnapshot
    }
  },
  { deep: true }
)
```

## 何时旧值和新值不同

```javascript
import { reactive, watch } from 'vue'

const state = reactive({
  currentUser: { name: 'John' }
})

watch(
  () => state.currentUser,
  (newUser, oldUser) => {
    // 当对象本身被替换时，这些不同
    console.log('旧：', oldUser)  // { name: 'John' }
    console.log('新：', newUser)  // { name: 'Jane' }
  },
  { deep: true }
)

// 对象替换 - 旧值和新值不同
state.currentUser = { name: 'Jane' }

// vs. 变异 - 旧值和新值是相同的引用
// state.currentUser.name = 'Jane'
```

## 使用 getter 返回新对象

```javascript
import { reactive, watch } from 'vue'

const state = reactive({
  user: { firstName: 'John', lastName: 'Doe' }
})

// 正确：getter 返回新对象，因此旧/新比较有效
watch(
  () => ({ ...state.user }),  // 浅拷贝
  (newUser, oldUser) => {
    // 现在它们是不同的对象
    console.log('从', oldUser, '更改为', newUser)
  },
  { deep: true }
)
```

## 参考
- [Vue.js 监听器 - 深度监听器](https://vuejs.org/guide/essentials/watchers.html#deep-watchers)
