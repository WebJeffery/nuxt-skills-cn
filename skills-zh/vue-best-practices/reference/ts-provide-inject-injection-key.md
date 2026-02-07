---
title: 使用 InjectionKey 实现类型安全的 Provide/Inject
impact: MEDIUM
impactDescription: 没有 InjectionKey，注入的值类型为 unknown，需要手动类型断言
type: best-practice
tags: [vue3, typescript, provide-inject, injection-key, composition-api]
---

# 使用 InjectionKey 实现类型安全的 Provide/Inject

**影响：中等** - 在 TypeScript 中使用 `provide/inject` 时，使用 `InjectionKey<T>` 配合 Symbol 实现类型安全的依赖注入。没有它，注入的值具有 `unknown` 类型或需要手动类型断言。

## 任务清单

- [ ] 使用 `Symbol() as InjectionKey<T>` 定义注入键
- [ ] 在共享文件中存储注入键，以便提供者/消费者访问
- [ ] 理解注入的值始终是 `T | undefined`
- [ ] 提供默认值以从类型中移除 undefined

## 问题所在

```vue
<!-- Provider.vue -->
<script setup lang="ts">
import { provide } from 'vue'

interface User {
  id: string
  name: string
}

const user: User = { id: '1', name: 'John' }

// 字符串键 - 没有类型信息
provide('user', user)
</script>
```

```vue
<!-- Consumer.vue -->
<script setup lang="ts">
import { inject } from 'vue'

// 类型是 unknown！
const user = inject('user')  // 类型: unknown

// 必须手动断言类型 - 容易出错
const user = inject('user') as User  // 可以工作但有风险
</script>
```

## 解决方案：InjectionKey

```typescript
// keys.ts - 共享注入键文件
import type { InjectionKey, Ref } from 'vue'

export interface User {
  id: string
  name: string
}

export interface AuthContext {
  user: Ref<User | null>
  login: (credentials: Credentials) => Promise<void>
  logout: () => Promise<void>
}

// 类型安全的注入键
export const userKey: InjectionKey<User> = Symbol('user')
export const authKey: InjectionKey<AuthContext> = Symbol('auth')
```

```vue
<!-- Provider.vue -->
<script setup lang="ts">
import { provide, ref } from 'vue'
import { userKey, authKey, type User, type AuthContext } from '@/keys'

const user: User = { id: '1', name: 'John' }

// 类型检查！必须提供正确的类型
provide(userKey, user)

// 如果类型不匹配则出错
provide(userKey, { wrong: 'data' })  // TypeScript 错误！

// 复杂的上下文
const authContext: AuthContext = {
  user: ref(null),
  login: async (creds) => { /* ... */ },
  logout: async () => { /* ... */ }
}
provide(authKey, authContext)
</script>
```

```vue
<!-- Consumer.vue -->
<script setup lang="ts">
import { inject } from 'vue'
import { userKey, authKey } from '@/keys'

// 自动类型化为 User | undefined
const user = inject(userKey)

// 类型: AuthContext | undefined
const auth = inject(authKey)

// 使用适当的类型访问
if (user) {
  console.log(user.name)  // TypeScript 知道这是 string
}
</script>
```

## 处理 Undefined 类型

注入的值始终可能是 `undefined`，因为提供者可能不存在：

```vue
<script setup lang="ts">
import { inject } from 'vue'
import { userKey, type User } from '@/keys'

// 选项 1：提供默认值（移除 undefined）
const user = inject(userKey, { id: '0', name: 'Guest' })
// 类型: User（不是 undefined）

// 选项 2：使用工厂函数作为默认值
const user = inject(userKey, () => ({ id: '0', name: 'Guest' }), true)
// 类型: User（仅当没有提供者时才调用工厂函数）

// 选项 3：在确定提供者存在时断言非空
const user = inject(userKey)!
// 类型: User（谨慎使用！）

// 选项 4：使用运行时检查进行保护
const user = inject(userKey)
if (!user) {
  throw new Error('User provider not found. Wrap component in UserProvider.')
}
// 检查后类型: User
</script>
```

## 创建 useInject Composable

为了更简洁的消费者代码，创建类型化的 composables：

```typescript
// composables/useAuth.ts
import { inject } from 'vue'
import { authKey, type AuthContext } from '@/keys'

export function useAuth(): AuthContext {
  const auth = inject(authKey)

  if (!auth) {
    throw new Error(
      'useAuth() requires an AuthProvider ancestor. ' +
      'Make sure to wrap your component tree with <AuthProvider>.'
    )
  }

  return auth
}
```

```vue
<!-- Consumer.vue -->
<script setup lang="ts">
import { useAuth } from '@/composables/useAuth'

// 清洁的 API，保证非空，适当的错误消息
const { user, login, logout } = useAuth()
</script>
```

## 组织注入键

对于大型应用程序，按领域组织键：

```
src/
├── injection-keys/
│   ├── index.ts        # 重新导出所有键
│   ├── auth.ts         # 认证相关键
│   ├── theme.ts        # 主题相关键
│   └── i18n.ts         # i18n 相关键
```

```typescript
// injection-keys/auth.ts
import type { InjectionKey, Ref } from 'vue'

export interface AuthState {
  isAuthenticated: Ref<boolean>
  user: Ref<User | null>
}

export interface AuthActions {
  login: (email: string, password: string) => Promise<void>
  logout: () => Promise<void>
  refresh: () => Promise<void>
}

export interface AuthContext extends AuthState, AuthActions {}

export const authStateKey: InjectionKey<AuthState> = Symbol('authState')
export const authActionsKey: InjectionKey<AuthActions> = Symbol('authActions')
export const authContextKey: InjectionKey<AuthContext> = Symbol('authContext')
```

```typescript
// injection-keys/index.ts
export * from './auth'
export * from './theme'
export * from './i18n'
```

## 泛型注入键

对于带泛型的可重用模式：

```typescript
// keys/list.ts
import type { InjectionKey, Ref } from 'vue'

export interface ListContext<T> {
  items: Ref<T[]>
  selectedItem: Ref<T | null>
  selectItem: (item: T) => void
}

// 创建类型化键的工厂函数
export function createListKey<T>(name: string): InjectionKey<ListContext<T>> {
  return Symbol(name) as InjectionKey<ListContext<T>>
}

// 使用
interface Product { id: string; name: string }
export const productListKey = createListKey<Product>('productList')
```

## 最佳实践总结

1. **始终使用 InjectionKey** 用于 TypeScript 项目
2. **使用 Symbol** 防止键冲突
3. **创建 composables** 如 `useAuth()` 以获得清洁的消费者 API
4. **抛出描述性错误** 当缺少必需的提供者时
5. **组织键** 在提供者和消费者可访问的共享位置
6. **提供默认值** 当提供者是可选的

## 参考
- [Vue.js TypeScript 与 Composition API - Provide/Inject](https://vuejs.org/guide/typescript/composition-api.html#typing-provide-inject)
- [Vue.js Provide/Inject](https://vuejs.org/guide/components/provide-inject.html)
