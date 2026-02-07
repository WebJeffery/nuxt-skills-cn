---
title: 首选基于类型的 defineProps 声明
impact: MEDIUM
impactDescription: 基于类型的声明提供更好的 TypeScript 集成和更简洁的语法
type: best-practice
tags: [vue3, typescript, props, defineProps, composition-api]
---

# 首选基于类型的 defineProps 声明

**影响：中** - Vue 3 支持两种使用 TypeScript 声明 props 的方式：运行时声明和基于类型的声明。推荐使用基于类型的声明，因为它提供更直接的 TypeScript 集成和更简洁的语法。

## 任务清单

- [ ] 对新的 TypeScript 组件使用基于类型的声明
- [ ] 提取 props 接口以实现可重用性和清晰性
- [ ] 使用 `withDefaults()` 设置默认值（Vue 3.4 及以下）
- [ ] 使用响应式 props 解构设置默认值（Vue 3.5+）
- [ ] 永远不要混合运行时和基于类型的声明

## 两种声明风格

**运行时声明（传统）：**
```vue
<script setup lang="ts">
const props = defineProps({
  foo: { type: String, required: true },
  bar: Number,
  items: { type: Array as PropType<string[]>, default: () => [] }
})
</script>
```

**基于类型的声明（推荐）：**
```vue
<script setup lang="ts">
interface Props {
  foo: string
  bar?: number
  items?: string[]
}

const props = defineProps<Props>()
</script>
```

## 不能混合两种风格

```typescript
// 错误：不能同时使用运行时和基于类型的声明
const props = defineProps<{ foo: string }>({
  foo: { type: String, required: true }  // 错误！
})

// 正确：选择一种风格
const props = defineProps<{ foo: string }>()  // 仅基于类型
// 或
const props = defineProps({                    // 仅运行时
  foo: { type: String, required: true }
})
```

## 基于类型声明的默认值

基于类型的声明需要 `withDefaults()` 宏来设置默认值（Vue 3.5 之前）：

```vue
<script setup lang="ts">
interface Props {
  msg?: string
  labels?: string[]
  config?: { theme: string }
}

// 关键：可变类型（数组、对象）必须使用工厂函数
const props = withDefaults(defineProps<Props>(), {
  msg: 'hello',
  labels: () => ['one', 'two'],      // 需要工厂函数！
  config: () => ({ theme: 'light' }) // 需要工厂函数！
})
</script>
```

## Vue 3.5+：响应式 Props 解构

在 Vue 3.5+ 中，直接使用解构和默认值：

```vue
<script setup lang="ts">
interface Props {
  msg?: string
  labels?: string[]
}

// Vue 3.5+ - 解构中的默认值
const { msg = 'hello', labels = ['one', 'two'] } = defineProps<Props>()
</script>
```

## 从外部文件导入类型

```typescript
// types/user.ts
export interface UserProps {
  id: string
  name: string
  email?: string
}
```

```vue
<script setup lang="ts">
import type { UserProps } from '@/types/user'

// 导入的类型与 defineProps 一起工作（Vue 3.3+）
const props = defineProps<UserProps>()
</script>
```

## 何时使用运行时声明

当你需要以下内容时，运行时声明仍然有用：

1. **运行时类型验证**，使用复杂的验证器函数
2. **Prop 类型强制**（例如，Boolean 强制转换行为）
3. **动态 prop 定义**，基于运行时条件

```vue
<script setup lang="ts">
import type { PropType } from 'vue'

// 用于复杂验证的运行时声明
const props = defineProps({
  status: {
    type: String as PropType<'active' | 'inactive'>,
    required: true,
    validator: (value: string) => ['active', 'inactive'].includes(value)
  }
})
</script>
```

## TypeScript 限制（Vue 3.3 之前）

在 Vue 3.2 及以下版本中，基于类型的声明仅支持：
- 内联定义的类型字面量
- 对本地接口的引用

Vue 3.3+ 支持：
- 导入的类型
- 有限的复杂类型
- 但不支持整个 props 对象的条件类型

```typescript
// Vue 3.3+ 支持此功能
import type { UserProps } from './types'
defineProps<UserProps>()

// 仍然不支持：整个 props 的条件类型
type ConditionalProps<T> = T extends string ? { foo: string } : { bar: number }
defineProps<ConditionalProps<SomeType>>()  // 错误！

// 单个 props 的条件类型是支持的
interface Props {
  value: SomeType extends string ? string : number  // OK
}
```

## 参考
- [Vue.js TypeScript 与 Composition API - Props](https://vuejs.org/guide/typescript/composition-api.html#typing-component-props)
- [Vue.js SFC Script Setup](https://vuejs.org/api/sfc-script-setup.html#defineprops-defineemits)
