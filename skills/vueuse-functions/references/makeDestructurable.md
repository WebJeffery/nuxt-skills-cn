---
category: Utilities
---

# makeDestructurable

使对象和数组同时具有同构可解构性。有关更多详细信息,请参阅[此博客](https://antfu.me/posts/destructuring-with-object-or-array/)。

## 用法

TypeScript 示例:

```ts twoslash include main
import { makeDestructurable } from '@vueuse/core'

const foo = { name: 'foo' }
const bar = 1024

const obj = makeDestructurable(
  { foo, bar } as const,
  [foo, bar] as const,
)
```

用法:

```ts twoslash
// @include: main
// ---cut---
let { foo, bar } = obj
let [foo, bar] = obj
```

## 类型声明

```ts
export declare function makeDestructurable<
  T extends Record<string, unknown>,
  A extends readonly any[],
>(obj: T, arr: A): T & A
```
