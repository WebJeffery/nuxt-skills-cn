---
title: 使用 immediate 选项而不是重复的初始调用
impact: LOW
impactDescription: 初始调用和 watch 回调的重复代码降低了可维护性
type: efficiency
tags: [vue3, watch, watchers, immediate, best-practices, DRY]
---

# 使用 immediate 选项而不是重复的初始调用

**影响：低** - 在 setup 时手动调用函数并在监听器中调用会导致重复代码。`immediate: true` 选项使用当前值立即运行监听器回调，结合了两种行为。

## 任务清单

- [ ] 使用 `{ immediate: true }` 而不是在 onMounted/created 中调用处理程序
- [ ] 考虑 `watchEffect` 作为替代方案（总是立即运行）
- [ ] 记住 immediate 回调在第一次运行时接收 `undefined` 作为 oldValue

**错误：**
```vue
<script setup>
import { ref, watch, onMounted } from 'vue'

const userId = ref(1)
const userData = ref(null)

// 错误：重复调用 - 一次在 onMounted，一次在 watch
async function loadUser(id) {
  const response = await fetch(`/api/users/${id}`)
  userData.value = await response.json()
}

onMounted(() => {
  loadUser(userId.value)  // 初始调用
})

watch(userId, (newId) => {
  loadUser(newId)  // 更改时
})
</script>
```

```javascript
// Options API - 错误：在 created 和 watch 中重复
export default {
  data() {
    return { userId: 1, userData: null }
  },
  created() {
    this.loadUser()  // 初始调用
  },
  watch: {
    userId() {
      this.loadUser()  // 更改时 - 重复！
    }
  },
  methods: {
    async loadUser() {
      const response = await fetch(`/api/users/${this.userId}`)
      this.userData = await response.json()
    }
  }
}
```

**正确：**
```vue
<script setup>
import { ref, watch } from 'vue'

const userId = ref(1)
const userData = ref(null)

// 正确：带有 immediate 的单个 watch 在挂载和更改时运行
watch(
  userId,
  async (newId) => {
    const response = await fetch(`/api/users/${newId}`)
    userData.value = await response.json()
  },
  { immediate: true }
)
</script>
```

```javascript
// Options API - 正确：使用 immediate 选项
export default {
  data() {
    return { userId: 1, userData: null }
  },
  watch: {
    userId: {
      async handler(newId) {
        const response = await fetch(`/api/users/${newId}`)
        this.userData = await response.json()
      },
      immediate: true
    }
  }
}
```

## 替代方案：watchEffect

```vue
<script setup>
import { ref, watchEffect } from 'vue'

const userId = ref(1)
const userData = ref(null)

// watchEffect 总是立即运行 - 不需要选项
watchEffect(async () => {
  const response = await fetch(`/api/users/${userId.value}`)
  userData.value = await response.json()
})
</script>
```

## 处理初始运行时的 oldValue

```vue
<script setup>
import { ref, watch } from 'vue'

const count = ref(0)

watch(
  count,
  (newVal, oldVal) => {
    // 在第一次运行时使用 immediate: true
    // oldVal 是 undefined
    if (oldVal === undefined) {
      console.log('初始运行，count 是：', newVal)
    } else {
      console.log(`Count 从 ${oldVal} 更改为 ${newVal}`)
    }
  },
  { immediate: true }
)
</script>
```

## 使用 once 选项的一次性 Watch

```vue
<script setup>
import { ref, watch } from 'vue'

const data = ref(null)

// 当数据可用时仅运行一次
watch(
  data,
  (value) => {
    if (value) {
      initializeWithData(value)
      // 监听器在此之后自动停止
    }
  },
  { once: true }
)
</script>
```

## 参考
- [Vue.js 监听器 - 急切监听器](https://vuejs.org/guide/essentials/watchers.html#eager-watchers)
