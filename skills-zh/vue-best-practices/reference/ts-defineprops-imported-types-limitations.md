---
title: defineProps 中导入的类型有限制
impact: MEDIUM
impactDescription: 复杂的导入类型（如条件类型）可能导致 defineProps 编译器错误
type: gotcha
tags: [vue3, typescript, defineProps, imported-types, vue3.3]
---

# defineProps 中导入的类型有限制

**影响：中等** - 虽然 Vue 3.3+ 支持在 `defineProps<>()` 中使用导入的类型，但某些复杂类型并未完全支持。条件类型、需要完整类型分析的映射类型和全局环境类型可能导致"无法解析的类型引用"错误。

## 任务清单

- [ ] 了解哪些类型模式是支持的，哪些是不支持的
- [ ] 保持 prop 类型定义简单明确
- [ ] 将复杂类型逻辑移到 defineProps 之外
- [ ] 显式导出接口，而不是使用全局声明

## 支持的模式（Vue 3.3+）

```typescript
// types/user.ts
export interface User {
  id: string
  name: string
  email?: string
}

export interface ListProps<T> {
  items: T[]
  selectedItem?: T
}

export type Status = 'pending' | 'active' | 'completed'
```

```vue
<script setup lang="ts">
import type { User, Status } from '@/types/user'

// 有效：简单的导入接口
defineProps<{
  user: User
}>()

// 有效：导入的联合类型
defineProps<{
  status: Status
}>()

// 有效：直接导入接口
defineProps<User>()
</script>
```

## 不支持的模式

### 整个 Props 对象的条件类型

```typescript
// types/conditional.ts
export type ConditionalProps<T> = T extends string
  ? { value: string, onChange: (v: string) => void }
  : { value: number, onChange: (v: number) => void }
```

```vue
<script setup lang="ts">
import type { ConditionalProps } from '@/types/conditional'

// 错误：整个 props 对象不支持条件类型
defineProps<ConditionalProps<string>>()
</script>
```

**变通方法：**
```vue
<script setup lang="ts">
// 直接定义解析的类型
interface StringProps {
  value: string
  onChange: (v: string) => void
}

defineProps<StringProps>()
</script>
```

### 单个 prop 的条件类型是支持的

```vue
<script setup lang="ts">
// 这有效 - 单个 prop 上的条件类型
interface Props {
  value: SomeType extends string ? string : number  // OK
}

defineProps<Props>()
</script>
```

### 全局环境类型

```typescript
// global.d.ts（没有 export 的环境声明）
interface GlobalUser {
  id: string
  name: string
}

// 没有 export 语句 - 这是一个环境声明
```

```vue
<script setup lang="ts">
// 错误："无法解析的类型引用"
defineProps<{
  user: GlobalUser  // 无法解析环境全局类型
}>()
</script>
```

**变通方法：**
```typescript
// types/user.ts - 使用显式 export
export interface GlobalUser {
  id: string
  name: string
}
```

```vue
<script setup lang="ts">
import type { GlobalUser } from '@/types/user'

// 有效：显式导入
defineProps<{
  user: GlobalUser
}>()
</script>
```

### 复杂的映射类型

```typescript
// types/complex.ts
export type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P]
}

export interface User {
  id: string
  profile: { name: string }
}
```

```vue
<script setup lang="ts">
import type { DeepReadonly, User } from '@/types/complex'

// 可能失败或产生不正确的类型
defineProps<{
  user: DeepReadonly<User>
}>()
</script>
```

**变通方法：**
```typescript
// 显式解析类型
export interface ReadonlyUser {
  readonly id: string
  readonly profile: { readonly name: string }
}
```

### 导入接口的联合

```typescript
// types/forms.ts
export interface TextInput { type: 'text', value: string }
export interface NumberInput { type: 'number', value: number }
```

```vue
<script setup lang="ts">
import type { TextInput, NumberInput } from '@/types/forms'

// 在某些 Vue 版本中可能导致问题
defineProps<{
  input: TextInput | NumberInput
}>()
</script>
```

**变通方法：**
```typescript
// 在类型文件中定义联合
export type AnyInput = TextInput | NumberInput
```

```vue
<script setup lang="ts">
import type { AnyInput } from '@/types/forms'

defineProps<{
  input: AnyInput
}>()
</script>
```

## 最佳实践

### 保持 Props 类型简单

```typescript
// 好：简单、明确的接口
export interface ButtonProps {
  variant: 'primary' | 'secondary' | 'danger'
  size: 'sm' | 'md' | 'lg'
  disabled?: boolean
  loading?: boolean
}

// 避免：过度设计的泛型类型
export interface ButtonProps<V extends string, S extends string> {
  variant: V
  size: S
  // ...复杂的类型体操
}
```

### 在导出之前解析类型

```typescript
// 不要导出泛型工具
// export type PartialBy<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>

// 导出解析的类型
export interface CreateUserProps {
  name: string
  email: string
  age?: number // 设为可选
  role?: 'admin' | 'user' // 设为可选
}
```

### 对复杂情况使用双脚本块

```vue
<script lang="ts">
// 常规脚本块用于复杂类型定义
import type { ComplexType } from '@/types'

// 在此处解析类型
type ResolvedProps = ComplexType extends SomeCondition
  ? { a: string }
  : { b: number }
</script>

<script setup lang="ts">
// 使用解析的类型
defineProps<ResolvedProps>()
</script>
```

## 版本特定行为

| Vue 版本 | 导入的类型 | 复杂类型 |
|-------------|---------------|---------------|
| 3.2 | 不支持 | 不支持 |
| 3.3 | 支持 | 有限支持 |
| 3.4+ | 支持 | 更好的支持 |

始终查看 Vue 更新日志以了解 defineProps 中类型支持的更新。

## 参考
- [Vue.js TypeScript 与 Composition API](https://vuejs.org/guide/typescript/composition-api.html)
- [GitHub Issue: defineProps with imported interfaces](https://github.com/vuejs/core/issues/8612)
- [GitHub Issue: Union types in defineProps](https://github.com/vuejs/core/issues/5804)
