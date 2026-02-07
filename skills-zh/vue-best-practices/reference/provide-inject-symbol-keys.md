---
title: 在大型应用中使用 Symbol 键进行 Provide/Inject
impact: MEDIUM
impactDescription: 字符串注入键在大型应用或使用第三方组件时可能发生冲突
type: best-practice
tags: [vue3, provide-inject, typescript, architecture, component-library]
---

# 在大型应用中使用 Symbol 键进行 Provide/Inject

**影响：中** - 使用字符串键进行 provide/inject 在小型应用中可以工作，但在大型应用、组件库或多个团队在同一个代码库上工作时可能导致键冲突。Symbol 键保证唯一性并提供更好的 TypeScript 集成。

## 任务清单

- [ ] 在大型应用中使用 Symbol 键进行 provide/inject
- [ ] 从专用键文件导出符号
- [ ] 使用 `InjectionKey<T>` 以获得 TypeScript 类型安全
- [ ] 仅将字符串键保留用于简单、本地用例

## 问题：字符串键冲突

**有风险 - 字符串键可能冲突：**
```vue
<!-- ThemeProvider.vue (你的代码) -->
<script setup>
import { provide, ref } from 'vue'
provide('theme', ref('dark'))
</script>

<!-- SomeThirdPartyComponent.vue (库) -->
<script setup>
import { provide, ref } from 'vue'
// 哎呀！相同的键，不同的值
provide('theme', ref({ primary: 'blue', secondary: 'gray' }))
</script>

<!-- DeepChild.vue -->
<script setup>
import { inject } from 'vue'
const theme = inject('theme') // 哪一个？最近的祖先获胜
</script>
```

## 解决方案：Symbol 键

**正确 - 唯一符号防止冲突：**

```js
// injection-keys.js
export const ThemeKey = Symbol('theme')
export const UserKey = Symbol('user')
export const ConfigKey = Symbol('config')
```

```vue
<!-- ThemeProvider.vue -->
<script setup>
import { provide, ref } from 'vue'
import { ThemeKey } from '@/injection-keys'

const theme = ref('dark')
provide(ThemeKey, theme)
</script>
```

```vue
<!-- ThemeConsumer.vue -->
<script setup>
import { inject } from 'vue'
import { ThemeKey } from '@/injection-keys'

const theme = inject(ThemeKey)
</script>
```

## TypeScript：InjectionKey 用于类型安全

Vue 提供 `InjectionKey<T>` 用于强类型注入：

```ts
// injection-keys.ts
import type { InjectionKey, Ref } from 'vue'

// 定义注入类型
interface User {
  id: string
  name: string
  email: string
}

interface ThemeConfig {
  mode: 'light' | 'dark'
  primaryColor: string
}

// 创建类型化注入键
export const UserKey: InjectionKey<Ref<User>> = Symbol('user')
export const ThemeKey: InjectionKey<Ref<ThemeConfig>> = Symbol('theme')
export const LoggerKey: InjectionKey<(msg: string) => void> = Symbol('logger')
```

```vue
<!-- Provider.vue -->
<script setup lang="ts">
import { provide, ref } from 'vue'
import { UserKey, ThemeKey } from '@/injection-keys'

const user = ref({
  id: '123',
  name: 'John',
  email: 'john@example.com'
})

const theme = ref({
  mode: 'dark' as const,
  primaryColor: '#007bff'
})

// TypeScript 验证提供的值与键的类型匹配
provide(UserKey, user)
provide(ThemeKey, theme)
</script>
```

```vue
<!-- Consumer.vue -->
<script setup lang="ts">
import { inject } from 'vue'
import { UserKey, ThemeKey } from '@/injection-keys'

// TypeScript 知道 user 是 Ref<User> | undefined
const user = inject(UserKey)

// 使用默认值，TypeScript 知道它不是 undefined
const theme = inject(ThemeKey, ref({ mode: 'light', primaryColor: '#000' }))

// 类型安全访问
console.log(user?.value.name) // TypeScript 知道形状
console.log(theme.value.mode) // 'light' | 'dark'
</script>
```

## 模式：注入键文件组织

对于大型应用，按功能组织键：

```
src/
  injection-keys/
    index.ts          # 重新导出所有键
    auth.ts           # 认证相关键
    theme.ts          # 主题相关键
    feature-x.ts      # 特定功能键
```

```ts
// injection-keys/auth.ts
import type { InjectionKey, Ref, ComputedRef } from 'vue'

export interface AuthState {
  user: User | null
  isAuthenticated: boolean
  permissions: string[]
}

export interface AuthActions {
  login: (credentials: Credentials) => Promise<void>
  logout: () => Promise<void>
  checkPermission: (permission: string) => boolean
}

export const AuthStateKey: InjectionKey<Ref<AuthState>> = Symbol('auth-state')
export const AuthActionsKey: InjectionKey<AuthActions> = Symbol('auth-actions')
```

```ts
// injection-keys/index.ts
export * from './auth'
export * from './theme'
export * from './feature-x'
```

## 使用类型处理缺失的注入

TypeScript 有助于捕获缺失的提供者：

```vue
<script setup lang="ts">
import { inject } from 'vue'
import { UserKey } from '@/injection-keys'

// 选项 1：显式处理 undefined
const user = inject(UserKey)
if (!user) {
  throw new Error('UserKey 必须由祖先组件提供')
}

// 选项 2：提供默认值
const userWithDefault = inject(UserKey, ref({
  id: 'guest',
  name: 'Guest User',
  email: ''
}))

// 选项 3：使用非空断言（仅当你确定时）
const userRequired = inject(UserKey)!
</script>
```

## 何时字符串键仍然可以

字符串键适用于：

- 具有少量提供者的小型应用
- 具有清晰边界的本地组件树
- 快速原型
- 具有唯一、命名空间字符串的应用级提供

```vue
<!-- 使用命名空间字符串的应用级提供 -->
<script setup>
import { provide } from 'vue'

// 命名空间字符串减少冲突风险
provide('myapp:config', config)
provide('myapp:analytics', analytics)
</script>
```

## 参考
- [Vue.js Provide/Inject - 使用 Symbol 键](https://vuejs.org/guide/components/provide-inject.html#working-with-symbol-keys)
- [Vue.js TypeScript - 类型化 Provide/Inject](https://vuejs.org/guide/typescript/composition-api.html#typing-provide-inject)
