---
name: composables-auto-imports
description: 自动导入的 Vue API、Nuxt 组合式函数和自定义工具
---

# 组合式函数自动导入

Nuxt 自动导入 Vue API、Nuxt 组合式函数和你的自定义组合式函数/工具。

## 内置自动导入

### Vue API

```vue
<script setup lang="ts">
// 无需导入 - 全部自动导入
const count = ref(0)
const doubled = computed(() => count.value * 2)

watch(count, (newVal) => {
  console.log('计数已更改:', newVal)
})

onMounted(() => {
  console.log('组件已挂载')
})

// 生命周期钩子
onBeforeMount(() => {})
onUnmounted(() => {})
onBeforeUnmount(() => {})

// 响应式
const state = reactive({ name: '张三' })
const shallow = shallowRef({ deep: 'object' })
</script>
```

### Nuxt 组合式函数

```vue
<script setup lang="ts">
// 全部自动导入
const route = useRoute()
const router = useRouter()
const config = useRuntimeConfig()
const appConfig = useAppConfig()
const nuxtApp = useNuxtApp()

// 数据获取
const { data } = await useFetch('/api/data')
const { data: asyncData } = await useAsyncData('key', () => fetchData())

// 状态
const state = useState('key', () => 'initial')
const cookie = useCookie('token')

// Head/SEO
useHead({ title: '我的页面' })
useSeoMeta({ description: '页面描述' })

// 请求助手 (SSR)
const headers = useRequestHeaders()
const event = useRequestEvent()
const url = useRequestURL()
</script>
```

## 自定义组合式函数 (`app/composables/`)

### 创建组合式函数

```ts
// composables/useCounter.ts
export function useCounter(initial = 0) {
  const count = ref(initial)
  const increment = () => count.value++
  const decrement = () => count.value--
  return { count, increment, decrement }
}
```

```ts
// composables/useAuth.ts
export function useAuth() {
  const user = useState<User | null>('user', () => null)
  const isLoggedIn = computed(() => !!user.value)

  async function login(credentials: Credentials) {
    user.value = await $fetch('/api/auth/login', {
      method: 'POST',
      body: credentials,
    })
  }

  async function logout() {
    await $fetch('/api/auth/logout', { method: 'POST' })
    user.value = null
  }

  return { user, isLoggedIn, login, logout }
}
```

### 使用组合式函数

```vue
<script setup lang="ts">
// 自动导入 - 无需 import 语句
const { count, increment } = useCounter(10)
const { user, isLoggedIn, login } = useAuth()
</script>
```

### 文件扫描规则

仅扫描顶层文件：

```
composables/
├── useAuth.ts         → useAuth() ✓
├── useCounter.ts      → useCounter() ✓
├── index.ts           → exports ✓
└── nested/
    └── helper.ts      → 不会自动导入 ✗
```

重新导出嵌套组合式函数：

```ts
// composables/index.ts
export { useHelper } from './nested/helper'
```

或配置扫描：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  imports: {
    dirs: [
      'composables',
      'composables/**', // 扫描所有嵌套
    ],
  },
})
```

## 工具函数 (`app/utils/`)

```ts
// utils/format.ts
export function formatDate(date: Date) {
  return date.toLocaleDateString()
}

export function formatCurrency(amount: number) {
  return new Intl.NumberFormat('zh-CN', {
    style: 'currency',
    currency: 'CNY',
  }).format(amount)
}
```

```vue
<script setup lang="ts">
// 自动导入
const date = formatDate(new Date())
const price = formatCurrency(99.99)
</script>
```

## 服务器工具 (`server/utils/`)

```ts
// server/utils/db.ts
export function useDb() {
  return createDbConnection()
}

// server/utils/auth.ts
export function verifyToken(token: string) {
  return jwt.verify(token, process.env.JWT_SECRET)
}
```

```ts
// server/api/users.ts
export default defineEventHandler(() => {
  const db = useDb() // 自动导入
  return db.query('SELECT * FROM users')
})
```

## 第三方包导入

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  imports: {
    presets: [
      {
        from: 'vue-i18n',
        imports: ['useI18n'],
      },
      {
        from: 'date-fns',
        imports: ['format', 'parseISO', 'differenceInDays'],
      },
      {
        from: '@vueuse/core',
        imports: ['useMouse', 'useWindowSize'],
      },
    ],
  },
})
```

## 显式导入

在需要时使用 `#imports` 别名：

```vue
<script setup lang="ts">
import { ref, computed, useFetch } from '#imports'
</script>
```

## 组合式函数上下文规则

Nuxt 组合式函数必须在有效上下文中调用：

```ts
// ❌ 错误 - 模块级别
const config = useRuntimeConfig()

export function useMyComposable() {}
```

```ts
// ✅ 正确 - 在函数内部
export function useMyComposable() {
  const config = useRuntimeConfig()
  return { apiBase: config.public.apiBase }
}
```

**有效上下文：**
- `<script setup>` 块
- `setup()` 函数
- `defineNuxtPlugin()` 回调
- `defineNuxtRouteMiddleware()` 回调

## 禁用自动导入

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  // 禁用所有自动导入
  imports: {
    autoImport: false,
  },

  // 或仅禁用目录扫描（保留 Vue/Nuxt 导入）
  imports: {
    scan: false,
  },
})
```

## 高级用法

### 组合式工厂函数

```ts
// composables/useAsyncFactory.ts
export function useAsyncFactory<T>(
  key: string,
  fetcher: () => Promise<T>
) {
  const { data, error, status } = useAsyncData(key, fetcher)
  
  const isLoading = computed(() => status.value === 'pending')
  const isError = computed(() => !!error.value)
  
  return {
    data,
    error,
    status,
    isLoading,
    isError,
    refresh: () => refreshNuxtData(key),
  }
}
```

### 依赖注入

```ts
// composables/useInjection.ts
import { provide, inject } from 'vue'

const INJECTION_KEY = Symbol('injection-key')

export function provideValue<T>(key: string, value: T) {
  const nuxtApp = useNuxtApp()
  nuxtApp.provide(key, value)
}

export function useInjection<T>(key: string): T {
  const nuxtApp = useNuxtApp()
  const value = nuxtApp.vueApp.config.globalProperties.$injections?.[key]
  
  if (!value) {
    throw new Error(`Injection key "${key}" not found`)
  }
  
  return value
}
```

### Composable 测试

```ts
// tests/composables/useCounter.test.ts
import { describe, it, expect } from 'vitest'
import { useCounter } from '~/composables/useCounter'

describe('useCounter', () => {
  it('increments correctly', () => {
    const { count, increment } = useCounter(0)
    
    expect(count.value).toBe(0)
    increment()
    expect(count.value).toBe(1)
  })
})
```

## 性能优化

### 减少依赖

```ts
// composables/useOptimized.ts
// 避免不必要的响应式追踪
export function useOptimized() {
  const nuxtApp = useNuxtApp()
  
  // 仅在需要时访问响应式数据
  const getData = () => {
    return nuxtApp._payload
  }
  
  return { getData }
}
```

### 记忆化结果

```ts
import { useMemoize } from '@vueuse/core'

export function useCachedFetch(url: string) {
  const fetcher = useMemoize(async (u: string) => {
    return await $fetch(u)
  })
  
  const data = fetcher(url)
  
  return { data }
}
```

## 类型安全

### 定义 Composable 类型

```ts
// composables/useForm.ts
interface FormOptions<T> {
  initialValues: T
  validate?: (values: T) => Record<string, string>
  onSubmit: (values: T) => Promise<void>
}

interface FormState<T> {
  values: T
  errors: Record<string, string>
  isSubmitting: boolean
  dirty: boolean
}

export function useForm<T extends Record<string, any>>(
  options: FormOptions<T>
): FormState<T> {
  const values = useState(`form-${Date.now()}`, () => ({ ...options.initialValues }))
  const errors = ref<Record<string, string>>({})
  const isSubmitting = ref(false)
  const dirty = ref(false)
  
  const validate = () => {
    errors.value = options.validate?.(values.value) || {}
    return Object.keys(errors.value).length === 0
  }
  
  const submit = async () => {
    if (!validate()) return
    
    isSubmitting.value = true
    try {
      await options.onSubmit(values.value)
    } finally {
      isSubmitting.value = false
    }
  }
  
  return {
    values: shallowReadonly(values),
    errors: shallowReadonly(errors),
    isSubmitting,
    dirty,
    validate,
    submit,
  }
}
```

## 最佳实践

1. **描述性命名** - 清晰表达 composable 的用途
2. **单一职责** - 每个 composable 做一件事
3. **类型安全** - 使用 TypeScript 类型
4. **SSR 安全** - 仅在有效上下文中调用
5. **返回引用** - 返回响应式数据而非值

## 常见问题

### Composable 未自动导入

**原因：** 不在 `composables/` 顶层目录

**解决：**

```ts
// composables/index.ts
export * from './nested/useHelper'
```

### 上下文错误

**原因：** 在模块级别调用 composable

**解决：**

```ts
// ❌ 错误
const config = useRuntimeConfig()

// ✅ 正确
export function useMyFeature() {
  const config = useRuntimeConfig()
  return { config }
}
```

### 水合不匹配

**原因：** 使用了浏览器专用 API

**解决：**

```ts
export function useFeature() {
  const isClient = import.meta.client
  
  const data = useState('data', () => {
    return isClient ? getBrowserData() : null
  })
  
  return { data }
}
```

<!--
Source references:
- https://nuxt.com/docs/guide/concepts/auto-imports
- https://nuxt.com/docs/directory-structure/app/composables
- https://nuxt.com/docs/directory-structure/app/utils
-->
