---
title: 不要在 reactive() 中使用泛型参数
impact: MEDIUM
impactDescription: 由于 ref 解包，泛型参数类型与实际返回类型不同，导致类型不匹配
type: gotcha
tags: [vue3, typescript, reactive, ref-unwrapping, composition-api]
---

# 不要在 reactive() 中使用泛型参数

**影响：中等** - 不建议使用 `reactive()` 的泛型参数，因为处理嵌套 ref 解包的返回类型与泛型参数类型不同。应该在变量上使用接口注解。

## 任务清单

- [ ] 在变量上使用类型注解，而不是泛型参数
- [ ] 理解 `reactive()` 会解包嵌套的 ref
- [ ] 对于泛型 composables，使用 `shallowRef` 或显式 `Ref<T>` 类型定义
- [ ] 对于简单值，优先使用 `ref()` 以避免这些问题

## 问题所在

```vue
<script setup lang="ts">
import { reactive, ref } from 'vue'

interface Book {
  title: string
  year: number
  author: Ref<string>  // 嵌套 ref
}

// 错误：泛型参数没有考虑 ref 解包
const book = reactive<Book>({
  title: 'Vue 3 Guide',
  year: 2024,
  author: ref('John Doe')
})

// TypeScript 认为 book.author 是 Ref<string>
// 但在运行时，它被解包为只是 string！
book.author.value  // TypeScript: OK, 运行时: ERROR (author 是 string，不是 ref)
</script>
```

## 解决方案：接口注解

```vue
<script setup lang="ts">
import { reactive, ref } from 'vue'

interface Book {
  title: string
  year?: number
}

// 正确：注解变量，而不是泛型
const book: Book = reactive({
  title: 'Vue 3 Guide'
})

book.title = 'New Title'  // TypeScript 知道这是 string
book.year = 2024         // TypeScript 知道这是 number | undefined
</script>
```

## 为什么会发生这种情况

当您使用 `reactive()` 时，Vue 会自动解包任何嵌套的 ref：

```typescript
import { reactive, ref, Ref } from 'vue'

const name = ref('John')
const state = reactive({
  name: name  // 这是一个 Ref<string>
})

// 在运行时，state.name 是 'John' (string)，而不是 Ref
console.log(state.name)       // 'John' (不是 ref 对象)
console.log(state.name.value) // 运行时错误：.value 不存在

// 实际返回类型与您预期的不同
// reactive<{ name: Ref<string> }>() 不会返回 { name: Ref<string> }
// 由于自动解包，它返回 { name: string }
```

## 正确的模式

### 模式 1：简单接口注解

```vue
<script setup lang="ts">
interface FormState {
  name: string
  email: string
  age: number
}

const form: FormState = reactive({
  name: '',
  email: '',
  age: 0
})
</script>
```

### 模式 2：使用 Partial 处理可选字段

```vue
<script setup lang="ts">
interface User {
  id: string
  name: string
  email: string
}

// 从部分数据开始
const user: Partial<User> = reactive({})

// 稍后填充
user.id = '123'
user.name = 'John'
</script>
```

### 模式 3：改用 ref()

对于更简单的情况，优先使用 `ref()`，它有更可预测的类型定义：

```vue
<script setup lang="ts">
interface User {
  id: string
  name: string
}

// ref() 与泛型配合良好
const user = ref<User>({
  id: '1',
  name: 'John'
})

// 使用 .value 访问 - 清晰且可预测
user.value.name = 'Jane'
</script>
```

## 泛型 Composables：使用 Ref<T> 或 shallowRef

在 composables 中使用泛型类型参数时：

```typescript
// 问题：带 ref() 的泛型 T 导致 UnwrapRef 问题
function useBroken<T>(initial: T) {
  const state = ref(initial)  // 类型变为 Ref<UnwrapRef<T>>
  state.value = initial       // 错误：T 不可赋值给 UnwrapRef<T>
  return state
}

// 解决方案 1：使用显式 Ref<T> 类型
function useFixed1<T>(initial: T) {
  const state: Ref<T> = ref(initial) as Ref<T>
  return state
}

// 解决方案 2：使用 shallowRef（无解包）
function useFixed2<T>(initial: T) {
  const state = shallowRef(initial)  // 正确类型化为 ShallowRef<T>
  return state
}
```

## 何时泛型参数是安全的

对于没有嵌套响应式的简单非 ref 值，泛型是安全的：

```typescript
// 安全：没有嵌套 ref
const state = reactive<{ count: number; name: string }>({
  count: 0,
  name: ''
})

// 也安全：显式简单类型
const list = reactive<string[]>([])
const map = reactive<Map<string, number>>(new Map())
```

问题只会在以下情况出现：
1. 您的接口中有嵌套的 Ref 类型
2. 您使用可能包含 ref 的泛型类型参数

## 参考
- [Vue.js TypeScript 与 Composition API - 定义 reactive() 类型](https://vuejs.org/guide/typescript/composition-api.html#typing-reactive)
- [GitHub Issue: ref with generic type](https://github.com/vuejs/core/discussions/9564)
- [Vue TypeScript Caveats Gist](https://gist.github.com/LinusBorg/e041ff635994b50b7cec9383c3a067f1)
