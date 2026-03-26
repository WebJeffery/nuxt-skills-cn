---
category: State
---

# useLocalStorage

响应式 [LocalStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)。

## 用法

请参阅 `useStorage`。

## Type Declarations

```ts
export declare function useLocalStorage(
  key: MaybeRefOrGetter<string>,
  initialValue: MaybeRefOrGetter<string>,
  options?: UseStorageOptions<string>,
): RemovableRef<string>
export declare function useLocalStorage(
  key: MaybeRefOrGetter<string>,
  initialValue: MaybeRefOrGetter<boolean>,
  options?: UseStorageOptions<boolean>,
): RemovableRef<boolean>
export declare function useLocalStorage(
  key: MaybeRefOrGetter<string>,
  initialValue: MaybeRefOrGetter<number>,
  options?: UseStorageOptions<number>,
): RemovableRef<number>
export declare function useLocalStorage<T>(
  key: MaybeRefOrGetter<string>,
  initialValue: MaybeRefOrGetter<T>,
  options?: UseStorageOptions<T>,
): RemovableRef<T>
export declare function useLocalStorage<T = unknown>(
  key: MaybeRefOrGetter<string>,
  initialValue: MaybeRefOrGetter<null>,
  options?: UseStorageOptions<T>,
): RemovableRef<T>
```
