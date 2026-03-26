---
category: State
related: useLocalStorage, useSessionStorage, useStorageAsync
---

# useStorage

Create a reactive ref that can be used to access & modify [LocalStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) or [SessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage).

Uses localStorage by default, other storage sources be specified via third argument.

## Usage

::: tip
当与 Nuxt 3 一起使用时，此函数将**不会**被自动导入，以支持 Nitro 内置的 [`useStorage()`](https://nitro.unjs.io/guide/storage)。如果要使用来自 VueUse 的函数，请使用显式导入。
:::

```ts
import { useStorage } from '@vueuse/core'

// bind object
const state = useStorage('my-store', { hello: 'hi', greeting: 'Hello' })

// bind boolean
const flag = useStorage('my-flag', true) // returns Ref<boolean>

// bind number
const count = useStorage('my-count', 0) // returns Ref<number>

// bind string with SessionStorage
const id = useStorage('my-id', 'some-string-id', sessionStorage) // returns Ref<string>

// delete data from storage
state.value = null
```

## Merge Defaults

默认情况下，如果存储中存在值，`useStorage` 将使用存储中的值并忽略默认值。请注意，当您向默认值添加更多属性时，如果客户端的存储没有该键，则该键可能为 `undefined`。

```ts
import { useStorage } from '@vueuse/core'
// ---cut---
localStorage.setItem('my-store', '{"hello": "hello"}')

const state = useStorage('my-store', { hello: 'hi', greeting: 'hello' }, localStorage)

console.log(state.value.greeting) // undefined，因为值未在存储中呈现
```

To solve that, you can enable `mergeDefaults` option.

```ts
import { useStorage } from '@vueuse/core'
// ---cut---
localStorage.setItem('my-store', '{"hello": "nihao"}')

const state = useStorage(
  'my-store',
  { hello: 'hi', greeting: 'hello' },
  localStorage,
  { mergeDefaults: true } // <--
)

console.log(state.value.hello) // 'nihao', from storage
console.log(state.value.greeting) // 'hello', from merged default value
```

When setting it to true, it will perform a **shallow merge** for objects. You can pass a function to perform custom merge (e.g. deep merge), for example:

```ts
import { useStorage } from '@vueuse/core'
// ---cut---
const state = useStorage(
  'my-store',
  { hello: 'hi', greeting: 'hello' },
  localStorage,
  { mergeDefaults: (storageValue, defaults) => deepMerge(defaults, storageValue) } // <--
)
```

## Custom Serialization

默认情况下，`useStorage` 将根据提供的默认值的数据类型智能地使用相应的序列化器。 For example, `JSON.stringify` / `JSON.parse` will be used for objects, `Number.toString` / `parseFloat` for numbers, etc.

You can also provide your own serialization function to `useStorage`:

```ts
import { useStorage } from '@vueuse/core'

useStorage(
  'key',
  {},
  undefined,
  {
    serializer: {
      read: (v: any) => v ? JSON.parse(v) : null,
      write: (v: any) => JSON.stringify(v),
    },
  },
)
```

请注意，当您提供 `null` 作为默认值时，`useStorage` 无法从中假设数据类型。在这种情况下，您可以提供自定义序列化器或显式重用内置序列化器。

```ts
import { StorageSerializers, useStorage } from '@vueuse/core'

const objectLike = useStorage('key', null, undefined, { serializer: StorageSerializers.object })
objectLike.value = { foo: 'bar' }
```

### Built-in Serializers

The following serializers are available via `StorageSerializers`:

| Type      | Description                           |
| --------- | ------------------------------------- |
| `string`  | Plain string                          |
| `number`  | Number (via `parseFloat`)             |
| `boolean` | Boolean                               |
| `object`  | JSON object/array                     |
| `map`     | JavaScript `Map`                      |
| `set`     | JavaScript `Set`                      |
| `date`    | JavaScript `Date` (via `toISOString`) |
| `any`     | Raw string passthrough                |

```ts
import { StorageSerializers, useStorage } from '@vueuse/core'

const myMap = useStorage('my-map', new Map(), undefined, {
  serializer: StorageSerializers.map,
})
```

## Options

```ts
useStorage('key', defaults, storage, {
  // Watch for deep changes in objects/arrays (default: true)
  deep: true,
  // Sync across tabs via storage events (default: true)
  listenToStorageChanges: true,
  // Write default value to storage if not present (default: true)
  writeDefaults: true,
  // Use shallowRef instead of ref (default: false)
  shallow: false,
  // Initialize only after component is mounted (default: false)
  initOnMounted: false,
  // Custom error handler (default: console.error)
  onError: e => console.error(e),
  // Watch flush timing (default: 'pre')
  flush: 'pre',
})
```

## Reactive Key

存储键可以是 ref 或 getter，当键更改时数据将更新：

```ts
import { useStorage } from '@vueuse/core'

const userId = ref('user-1')
const userData = useStorage(
  () => `user-data-${userId.value}`,
  { name: '' },
)

// 更改键将从新的存储位置读取
userId.value = 'user-2'
```

## Type Declarations

```ts
export interface Serializer<T> {
  read: (raw: string) => T
  write: (value: T) => string
}
export interface SerializerAsync<T> {
  read: (raw: string) => Awaitable<T>
  write: (value: T) => Awaitable<string>
}
export declare const StorageSerializers: Record<
  "boolean" | "object" | "number" | "any" | "string" | "map" | "set" | "date",
  Serializer<any>
>
export declare const customStorageEventName = "vueuse-storage"
export interface StorageEventLike {
  storageArea: StorageLike | null
  key: StorageEvent["key"]
  oldValue: StorageEvent["oldValue"]
  newValue: StorageEvent["newValue"]
}
export interface UseStorageOptions<T>
  extends ConfigurableEventFilter, ConfigurableWindow, ConfigurableFlush {
  /**
   * Watch for deep changes
   *
   * @default true
   */
  deep?: boolean
  /**
   * Listen to storage changes, useful for multiple tabs application
   *
   * @default true
   */
  listenToStorageChanges?: boolean
  /**
   * Write the default value to the storage when it does not exist
   *
   * @default true
   */
  writeDefaults?: boolean
  /**
   * Merge the default value with the value read from the storage.
   *
   * When setting it to true, it will perform a **shallow merge** for objects.
   * You can pass a function to perform custom merge (e.g. deep merge), for example:
   *
   * @default false
   */
  mergeDefaults?: boolean | ((storageValue: T, defaults: T) => T)
  /**
   * Custom data serialization
   */
  serializer?: Serializer<T>
  /**
   * On error callback
   *
   * Default log error to `console.error`
   */
  onError?: (error: unknown) => void
  /**
   * Use shallow ref as reference
   *
   * @default false
   */
  shallow?: boolean
  /**
   * Wait for the component to be mounted before reading the storage.
   *
   * @default false
   */
  initOnMounted?: boolean
}
export declare function useStorage(
  key: MaybeRefOrGetter<string>,
  defaults: MaybeRefOrGetter<string>,
  storage?: StorageLike,
  options?: UseStorageOptions<string>,
): RemovableRef<string>
export declare function useStorage(
  key: MaybeRefOrGetter<string>,
  defaults: MaybeRefOrGetter<boolean>,
  storage?: StorageLike,
  options?: UseStorageOptions<boolean>,
): RemovableRef<boolean>
export declare function useStorage(
  key: MaybeRefOrGetter<string>,
  defaults: MaybeRefOrGetter<number>,
  storage?: StorageLike,
  options?: UseStorageOptions<number>,
): RemovableRef<number>
export declare function useStorage<T>(
  key: MaybeRefOrGetter<string>,
  defaults: MaybeRefOrGetter<T>,
  storage?: StorageLike,
  options?: UseStorageOptions<T>,
): RemovableRef<T>
export declare function useStorage<T = unknown>(
  key: MaybeRefOrGetter<string>,
  defaults: MaybeRefOrGetter<null>,
  storage?: StorageLike,
  options?: UseStorageOptions<T>,
): RemovableRef<T>
```
