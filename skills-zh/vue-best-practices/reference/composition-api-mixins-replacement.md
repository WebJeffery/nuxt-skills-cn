---
title: 使用组合式函数而不是 Mixins 进行逻辑重用
impact: HIGH
impactDescription: Mixins 导致命名冲突、数据来源不明确和逻辑不灵活 - 组合式函数解决了所有这些问题
type: best-practice
tags: [vue3, composition-api, composables, mixins, refactoring, code-reuse]
---

# 使用组合式函数而不是 Mixins 进行逻辑重用

**影响:高** - Mixins 是选项 API 中主要的逻辑重用机制,存在根本性缺陷,使代码难以维护。组合式函数(组合式 API 函数)解决了所有 Mixins 的缺点:属性来源不明确、命名冲突和无法参数化。

通过组合式函数创建干净、可重用的逻辑的能力是组合式 API 的主要优势。

## 任务清单

- [ ] 重构时将现有的 Mixins 迁移到组合式函数
- [ ] 永远不要创建新的 Mixins - 改用组合式函数
- [ ] 使用显式导入使数据来源清晰
- [ ] 参数化组合式函数使其灵活
- [ ] 组合式函数使用 "use" 前缀(useAuth、useFetch、useForm)

**Mixins 的问题:**
```javascript
// userMixin.js
export const userMixin = {
  data() {
    return {
      user: null,
      loading: false // 等待冲突!
    }
  },
  methods: {
    fetchUser() { /* ... */ }
  }
}

// authMixin.js
export const authMixin = {
  data() {
    return {
      token: null,
      loading: false // 与 userMixin 命名冲突!
    }
  },
  methods: {
    login() { /* ... */ }
  }
}

// 使用 Mixins 的组件 - 有问题
export default {
  mixins: [userMixin, authMixin],

  mounted() {
    // 问题 1: 'user' 来自哪里?必须检查 Mixins
    console.log(this.user)

    // 问题 2: 哪个 'loading'?最后一个 Mixins 获胜,静默!
    console.log(this.loading) // 这是用户加载还是身份验证加载?

    // 问题 3: 无法按组件自定义行为
    this.fetchUser() // 总是以相同方式获取
  }
}
```

**组合式函数解决方案:**
```javascript
// composables/useUser.js
import { ref } from 'vue'

export function useUser(userId) {  // 可以接受参数!
  const user = ref(null)
  const loading = ref(false)
  const error = ref(null)

  async function fetchUser() {
    loading.value = true
    try {
      user.value = await api.getUser(userId)
    } catch (e) {
      error.value = e
    } finally {
      loading.value = false
    }
  }

  return { user, loading, error, fetchUser }
}

// composables/useAuth.js
import { ref } from 'vue'

export function useAuth() {
  const token = ref(null)
  const loading = ref(false)  // 没有冲突 - 它是作用域的!

  async function login(credentials) { /* ... */ }
  function logout() { /* ... */ }

  return { token, loading, login, logout }
}

// 使用组合式函数的组件 - 清晰且灵活
<script setup>
import { useUser } from '@/composables/useUser'
import { useAuth } from '@/composables/useAuth'

// 解决方案 1: 清楚一切来自哪里
const { user, loading: userLoading, fetchUser } = useUser(123)
const { token, loading: authLoading, login } = useAuth()

// 解决方案 2: 重命名以避免任何冲突
// userLoading vs authLoading - 显式!

// 解决方案 3: 参数化行为
const adminUser = useUser(adminId)
const currentUser = useUser(currentUserId)
// 每个都有自己的状态!

onMounted(() => {
  fetchUser()  // 显式来自 useUser
})
</script>
```

## 从 Mixins 迁移

```javascript
// 之前: 带选项的 Mixin
export const formMixin = {
  data() {
    return { errors: {}, submitting: false }
  },
  methods: {
    validate() { /* ... */ },
    submit() { /* ... */ }
  }
}

// 之后: 带灵活性的组合式函数
export function useForm(initialValues, validationSchema) {
  const values = ref({ ...initialValues })
  const errors = ref({})
  const submitting = ref(false)
  const touched = ref({})

  function validate() {
    errors.value = validationSchema.validate(values.value)
    return Object.keys(errors.value).length === 0
  }

  async function submit(onSubmit) {
    if (!validate())
      return

    submitting.value = true
    try {
      await onSubmit(values.value)
    }
    finally {
      submitting.value = false
    }
  }

  function reset() {
    values.value = { ...initialValues }
    errors.value = {}
    touched.value = {}
  }

  return {
    values,
    errors,
    submitting,
    touched,
    validate,
    submit,
    reset
  }
}

// 用法 - 现在可参数化且显式
const loginForm = useForm(
  { email: '', password: '' },
  loginValidationSchema
)

const registerForm = useForm(
  { email: '', password: '', name: '' },
  registerValidationSchema
)
```

## 组合优于 Mixins 的好处

| 方面 | Mixins | 组合式函数 |
|--------|--------|-------------|
| 属性来源 | 不明确 | 显式导入 |
| 命名冲突 | 静默覆盖 | 显式重命名 |
| 参数 | 不可能 | 完全支持 |
| 类型推断 | 差 | 优秀 |
| 重用实例 | 每个组件一个 | 允许多个 |
| Tree-shaking | 不可能 | 完全支持 |

## 参考
- [组合式 API FAQ - 更好的逻辑重用](https://vuejs.org/guide/extras/composition-api-faq.html#better-logic-reuse)
- [组合式函数](https://vuejs.org/guide/reusability/composables.html)
- [VueUse - 组合式函数集合](https://vueuse.org/)
