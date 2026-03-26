---
category: '@Firebase'
---

# useFirestore

响应式 [Firestore](https://firebase.google.com/docs/firestore) 绑定。使**始终将本地数据与远程数据库保持同步**变得简单。

## 用法

```ts {9,12,17,22}
import { useFirestore } from '@vueuse/firebase/useFirestore'
import { initializeApp } from 'firebase/app'
import { collection, doc, getFirestore, limit, orderBy, query } from 'firebase/firestore'
import { computed, shallowRef } from 'vue'

const app = initializeApp({ projectId: 'MY PROJECT ID' })
const db = getFirestore(app)

const todos = useFirestore(collection(db, 'todos'))

// 或用于文档引用
const user = useFirestore(doc(db, 'users', 'my-user-id'))

// 您还可以使用 ref 值进行响应式查询
const postsLimit = shallowRef(10)
const postsQuery = computed(() => query(collection(db, 'posts'), orderBy('createdAt', 'desc'), limit(postsLimit.value)))
const posts = useFirestore(postsQuery)

// 您可以使用布尔值来告诉查询何时准备好运行
// 当它获取假值时，返回初始值
const userId = shallowRef('')
const userQuery = computed(() => userId.value && doc(db, 'users', userId.value))
const userData = useFirestore(userQuery, null)
```

### 返回值

- 对于**文档引用**：返回 `Ref<T | null>`（单个文档，带有 `id` 属性）
- 对于**查询**：返回 `Ref<T[]>`（文档数组，每个都有 `id` 属性）

文档 `id` 作为只读属性自动添加到每个返回的文档。

### 选项

| 选项         | 类型                   | 默认值         | 描述                                                                |
| -------------- | ---------------------- | --------------- | -------------------------------------------------------------------------- |
| `errorHandler` | `(err: Error) => void` | `console.error` | 自定义错误处理程序                                                       |
| `autoDispose`  | `boolean \| number`    | `true`          | 在作用域释放时自动取消订阅。传递数字以延迟释放（毫秒）。 |

### 错误处理

```ts
const todos = useFirestore(collection(db, 'todos'), [], {
  errorHandler: (err) => {
    console.error('Firestore error:', err)
    // 处理错误（例如，显示通知）
  },
})
```

## 跨实例共享

您可以通过传递 `autoDispose: false` 来重用 db 引用。您还可以设置 db 引用自动释放的毫秒数。

注意：再次获取未释放的 db 引用不会消耗 Firestore 读取。

```ts
import { useFirestore } from '@vueuse/firebase/useFirestore'
import { collection } from 'firebase/firestore'
// ---cut---
const todos = useFirestore(collection(db, 'todos'), undefined, { autoDispose: false })
```

或使用核心包中的 `createGlobalState`

```ts twoslash include store
// @filename: store.ts
// ---cut---
// store.ts
import { createGlobalState } from '@vueuse/core'
import { useFirestore } from '@vueuse/firebase/useFirestore'

export const useTodos = createGlobalState(
  () => useFirestore(collection(db, 'todos')),
)
```

```vue
<!-- app.vue -->
<script setup lang="ts">
// @include: store
// ---cut---
import { useTodos } from './store'

const todos = useTodos()
</script>
```

## 类型声明

```ts
export interface UseFirestoreOptions {
  errorHandler?: (err: Error) => void
  autoDispose?: boolean | number
}
export type FirebaseDocRef<T> = Query<T> | DocumentReference<T>
type Falsy = false | 0 | "" | null | undefined
export declare function useFirestore<T extends DocumentData>(
  maybeDocRef: MaybeRef<DocumentReference<T> | Falsy>,
  initialValue: T,
  options?: UseFirestoreOptions,
): Ref<T | null>
export declare function useFirestore<T extends DocumentData>(
  maybeDocRef: MaybeRef<Query<T> | Falsy>,
  initialValue: T[],
  options?: UseFirestoreOptions,
): Ref<T[]>
export declare function useFirestore<T extends DocumentData>(
  maybeDocRef: MaybeRef<DocumentReference<T> | Falsy>,
  initialValue?: T | undefined | null,
  options?: UseFirestoreOptions,
): Ref<T | undefined | null>
export declare function useFirestore<T extends DocumentData>(
  maybeDocRef: MaybeRef<Query<T> | Falsy>,
  initialValue?: T[],
  options?: UseFirestoreOptions,
): Ref<T[] | undefined>
```
