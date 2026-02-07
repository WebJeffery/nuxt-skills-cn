---
title: 确保在计算属性中访问所有依赖项
impact: HIGH
impactDescription: 条件逻辑可能会阻止依赖跟踪，导致计算值过时
type: capability
tags: [vue3, computed, reactivity, dependency-tracking, gotcha]
---

# 确保在计算属性中访问所有依赖项

**影响：高** - Vue 通过监视在执行期间访问哪些响应式属性来跟踪计算属性依赖项。如果条件逻辑阻止在首次运行时访问属性，Vue 不会将其跟踪为依赖项，导致计算属性在该属性更改时不更新。

这是一个微妙但常见的错误来源，特别是使用短路求值（`&&`、`||`）和提前返回时。

## 任务清单

- [ ] 在任何条件逻辑之前访问所有响应式依赖项
- [ ] 谨慎使用可能跳过属性访问的短路运算符（`&&`、`||`）
- [ ] 在计算属性 getter 开始时将所有依赖项存储在变量中
- [ ] 使用不同的初始状态测试计算属性

**错误：**
```vue
<script setup>
import { ref, computed } from 'vue'

const isEnabled = ref(false)
const data = ref('important data')

// 错误：如果 isEnabled 最初为 false，则永远不会访问 data.value
// Vue 不会将 'data' 跟踪为依赖项！
const result = computed(() => {
  if (!isEnabled.value) {
    return 'disabled'
  }
  return data.value  // 此依赖项可能不会被跟踪
})

// 错误：短路阻止第二次访问
const password = ref('')
const confirmPassword = ref('')

const isValid = computed(() => {
  // 如果 password 为空，则永远不会访问 confirmPassword
  return password.value && password.value === confirmPassword.value
})

// 错误：提前返回阻止依赖项访问
const user = ref(null)
const permissions = ref(['read', 'write'])

const canEdit = computed(() => {
  if (!user.value) {
    return false  // 当 user 为 null 时永远不会访问 permissions.value
  }
  return permissions.value.includes('write')
})
</script>
```

**正确：**
```vue
<script setup>
import { ref, computed } from 'vue'

const isEnabled = ref(false)
const data = ref('important data')

// 正确：首先访问所有依赖项
const result = computed(() => {
  const enabled = isEnabled.value
  const currentData = data.value  // 始终被访问

  if (!enabled) {
    return 'disabled'
  }
  return currentData
})

// 正确：在比较之前访问两个值
const password = ref('')
const confirmPassword = ref('')

const isValid = computed(() => {
  const pwd = password.value
  const confirm = confirmPassword.value  // 始终被访问

  return pwd && pwd === confirm
})

// 正确：预先访问所有响应式源
const user = ref(null)
const permissions = ref(['read', 'write'])

const canEdit = computed(() => {
  const currentUser = user.value
  const currentPermissions = permissions.value  // 始终被访问

  if (!currentUser) {
    return false
  }
  return currentPermissions.includes('write')
})
</script>
```

## 依赖跟踪机制

Vue 的响应式系统通过跟踪计算属性运行时访问哪些响应式属性来工作：

```javascript
// Vue 如何跟踪依赖项（简化）：
// 1. 开始跟踪
// 2. 运行 getter 函数
// 3. 记录每个 .value 或响应式属性访问
// 4. 停止跟踪

const computed = computed(() => {
  // Vue 在这里开始跟踪
  if (conditionA.value) {      // conditionA 被跟踪
    return valueB.value        // 只有当 conditionA 为 true 时才跟踪 valueB
  }
  return 'default'             // 如果 conditionA 为 false，valueB 不会被跟踪！
})
```

## 模式：首先解构所有依赖项

```javascript
// 好模式：在顶部解构/访问所有内容
const result = computed(() => {
  // 访问所有潜在依赖项
  const { user, settings, items } = toRefs(store)
  const userVal = user.value
  const settingsVal = settings.value
  const itemsVal = items.value

  // 现在可以安全地使用条件逻辑
  if (!userVal) return []
  if (!settingsVal.enabled) return []
  return itemsVal.filter(i => i.active)
})
```

## 参考
- [Vue.js 响应式深入](https://vuejs.org/guide/extras/reactivity-in-depth.html)
- [GitHub 讨论：条件依赖项收集陷阱](https://github.com/vuejs/Discussion/issues/15)
