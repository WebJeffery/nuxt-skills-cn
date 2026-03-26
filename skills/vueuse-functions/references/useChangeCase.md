---
category: '@Integrations'
---

# useChangeCase

[`change-case`](https://github.com/blakeembrey/change-case) 的响应式包装器。

替代 `useCamelCase`、`usePascalCase`、`useSnakeCase`、`useSentenceCase`、`useCapitalize` 等。

## 安装

```bash
npm i change-case@^5
```

## 用法

```ts
import { useChangeCase } from '@vueuse/integrations/useChangeCase'

// `changeCase` 将是一个 computed
const changeCase = useChangeCase('hello world', 'camelCase')
changeCase.value // helloWorld
changeCase.value = 'vue use'
changeCase.value // vueUse
// 支持的方法
// export {
//   camelCase,
//   capitalCase,
//   constantCase,
//   dotCase,
//   headerCase,
//   noCase,
//   paramCase,
//   pascalCase,
//   pathCase,
//   sentenceCase,
//   snakeCase,
// } from 'change-case'
```

或者将 `ref` 传递给它,返回的 `computed` 将随着源 ref 的更改而更改。

可以传递到 `options` 进行自定义

```ts
import { useChangeCase } from '@vueuse/integrations/useChangeCase'
import { shallowRef } from 'vue'

const input = shallowRef('helloWorld')
const changeCase = useChangeCase(input, 'camelCase', {
  delimiter: '-',
})
changeCase.value // hello-World
input.value = 'vue use'
changeCase.value // vue-Use
```

## 类型声明

```ts
type EndsWithCase<T> = T extends `${infer _}Case` ? T : never
type FilterKeys<T> = {
  [K in keyof T as K extends string ? K : never]: EndsWithCase<K>
}
type ChangeCaseKeys = FilterKeys<typeof changeCase>
export type ChangeCaseType = ChangeCaseKeys[keyof ChangeCaseKeys]
export declare function useChangeCase(
  input: MaybeRef<string>,
  type: MaybeRefOrGetter<ChangeCaseType>,
  options?: MaybeRefOrGetter<Options> | undefined,
): WritableComputedRef<string>
export declare function useChangeCase(
  input: MaybeRefOrGetter<string>,
  type: MaybeRefOrGetter<ChangeCaseType>,
  options?: MaybeRefOrGetter<Options> | undefined,
): ComputedRef<string>
```
