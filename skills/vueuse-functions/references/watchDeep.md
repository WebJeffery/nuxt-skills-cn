---
category: Watch
---

# watchDeep

使用 `{deep: true}` 监视值的简写

## 用法

与 `watch` 类似，但使用 `{ deep: true }`

```ts
import { watchDeep } from '@vueuse/core'

const nestedObject = ref({ foo: { bar: { deep: 5 } } })

watchDeep(nestedObject, (updated) => {
  console.log(updated)
})

onMounted(() => {
  nestedObject.value.foo.bar.deep = 10
})
```

## 类型声明

```ts
export declare function watchDeep<
  T extends Readonly<MultiWatchSources>,
  Immediate extends Readonly<boolean> = false,
>(
  source: [...T],
  cb: WatchCallback<MapSources<T>, MapOldSources<T, Immediate>>,
  options?: Omit<WatchOptions<Immediate>, "deep">,
): WatchHandle
export declare function watchDeep<
  T,
  Immediate extends Readonly<boolean> = false,
>(
  source: WatchSource<T>,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: Omit<WatchOptions<Immediate>, "deep">,
): WatchHandle
export declare function watchDeep<
  T extends object,
  Immediate extends Readonly<boolean> = false,
>(
  source: T,
  cb: WatchCallback<T, Immediate extends true ? T | undefined : T>,
  options?: Omit<WatchOptions<Immediate>, "deep">,
): WatchHandle
```
