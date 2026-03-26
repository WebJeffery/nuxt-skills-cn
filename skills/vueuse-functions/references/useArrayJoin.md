---
name: useArrayJoin
description: 响应式数组的 join 实现
---

# useArrayJoin

`useArrayJoin` 是 `Array.prototype.join` 的响应式版本。它返回一个计算属性，包含数组所有元素连接而成的字符串。

## 用法

```ts
import { useArrayJoin } from '@vueuse/core'

const list = ref(['Hello', 'World', 'VueUse'])

const result = useArrayJoin(list)
console.log(result.value) // 'Hello,World,VueUse'

const result2 = useArrayJoin(list, ' ')
console.log(result2.value) // 'Hello World VueUse'
```

## 类型定义

```ts
export type UseArrayJoinReturn = ComputedRef<string>

export declare function useArrayJoin(
  list: MaybeRefOrGetter<MaybeRefOrGetter<any>[]>,
  separator?: MaybeRefOrGetter<string>,
): UseArrayJoinReturn
```

## 参数

- `list`: 要连接的数组，可以是 ref、getter 或普通数组
- `separator`: 用于分隔数组元素的字符串，默认为逗号

## 返回值

返回一个计算属性，包含数组所有元素连接而成的字符串。

<!--
Source references:
- https://vueuse.org/core/useArrayJoin/
-->