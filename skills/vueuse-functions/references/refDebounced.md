---
category: Reactivity
alias: useDebounce, debouncedRef
---

# refDebounced

防抖 ref 值的执行。

## 用法

```ts {5}
import { refDebounced } from '@vueuse/core'
import { shallowRef } from 'vue'

const input = shallowRef('foo')
const debounced = refDebounced(input, 1000)

input.value = 'bar'
console.log(debounced.value) // 'foo'

await sleep(1100)

console.log(debounced.value) // 'bar'
// ---cut-after---
function sleep(ms: number) {
  return new Promise(resolve => setTimeout(resolve, ms))
}
```

对象 ref 的示例。

```js
import { refDebounced } from '@vueuse/core'
import { shallowRef } from 'vue'

const data = shallowRef({
  name: 'foo',
  age: 18,
})
const debounced = refDebounced(data, 1000)

function update() {
  data.value = {
    ...data.value,
    name: 'bar',
  }
}

console.log(debounced.value) // { name: 'foo', age: 18 }
update()
await sleep(1100)

console.log(debounced.value) // { name: 'bar', age: 18 }
```

您还可以传递包括 maxWait 选项的可选第 3 个参数。有关详细信息,请参阅 `useDebounceFn`。

## 推荐阅读

- [**防抖 vs 节流**: 决定性视觉指南](https://kettanaito.com/blog/debounce-vs-throttle)

## 类型声明

```ts
export type RefDebouncedReturn<T = any> = Readonly<Ref<T>>
/**
 * 防抖 ref 的更新。
 *
 * @return 一个新的防抖 ref。
 */
export declare function refDebounced<T>(
  value: Ref<T>,
  ms?: MaybeRefOrGetter<number>,
  options?: DebounceFilterOptions,
): RefDebouncedReturn<T>
/** @deprecated 使用 `refDebounced` 代替 */
export declare const debouncedRef: typeof refDebounced
/** @deprecated 使用 `refDebounced` 代替 */
export declare const useDebounce: typeof refDebounced
```
