---
category: State
---

# createInjectionState

创建可以注入到组件中的全局状态。

## 用法

```ts twoslash include useCounterStore
// useCounterStore.ts
import { createInjectionState } from '@vueuse/core'
import { computed, shallowRef } from 'vue'

const [useProvideCounterStore, useCounterStore] = createInjectionState((initialValue: number) => {
  // 状态
  const count = shallowRef(initialValue)

  // getters
  const double = computed(() => count.value * 2)

  // actions
  function increment() {
    count.value++
  }

  return { count, double, increment }
})

export { useProvideCounterStore }

// 如果您想隐藏 `useCounterStore` 并将其包装在默认值逻辑或抛出错误逻辑中,请不要导出 `useCounterStore`
export { useCounterStore }

export function useCounterStoreWithDefaultValue() {
  return useCounterStore() ?? {
    count: shallowRef(0),
    double: shallowRef(0),
    increment: () => {},
  }
}

export function useCounterStoreOrThrow() {
  const counterStore = useCounterStore()
  if (counterStore == null)
    throw new Error('Please call `useProvideCounterStore` on appropriate parent component')
  return counterStore
}
```

```vue
<!-- RootComponent.vue -->
<script setup lang="ts">
// @filename: useCounterStore.ts
// @include: useCounterStore
// ---cut---
import { useProvideCounterStore } from './useCounterStore'

useProvideCounterStore(0)
</script>

<template>
  <div>
    <slot />
  </div>
</template>
```

```vue
<!-- CountComponent.vue -->
<script setup lang="ts">
// @filename: useCounterStore.ts
// @include: useCounterStore
// ---cut---
import { useCounterStore } from './useCounterStore'

// 使用非空断言运算符来忽略未提供 store 的情况。
const { count, double } = useCounterStore()!
// 如果您想允许组件在没有提供 store 的情况下工作,可以使用以下代码代替:
// const { count, double } = useCounterStore() ?? { count: shallowRef(0), double: shallowRef(0) }
// 也可以,您可以使用另一个 hook 来提供默认值
// const { count, double } = useCounterStoreWithDefaultValue()
// 或者抛出错误
// const { count, double } = useCounterStoreOrThrow()
</script>

<template>
  <ul>
    <li>
      count: {{ count }}
    </li>
    <li>
      double: {{ double }}
    </li>
  </ul>
</template>
```

```vue
<!-- ButtonComponent.vue -->
<script setup lang="ts">
// @filename: useCounterStore.ts
// @include: useCounterStore
// ---cut---
import { useCounterStore } from './useCounterStore'

// 使用非空断言运算符来忽略未提供 store 的情况。
const { increment } = useCounterStore()!
</script>

<template>
  <button @click="increment">
    +
  </button>
</template>
```

## 提供自定义 InjectionKey

```ts
// useCounterStore.ts
import { createInjectionState } from '@vueuse/core'
import { computed, shallowRef } from 'vue'

// 自定义 injectionKey
const CounterStoreKey = 'counter-store'

const [useProvideCounterStore, useCounterStore] = createInjectionState((initialValue: number) => {
  // 状态
  const count = shallowRef(initialValue)

  // getters
  const double = computed(() => count.value * 2)

  // actions
  function increment() {
    count.value++
  }

  return { count, double, increment }
}, { injectionKey: CounterStoreKey })
```

## 提供自定义默认值

```ts
// useCounterStore.ts
import { createInjectionState } from '@vueuse/core'
import { computed, shallowRef } from 'vue'

const [useProvideCounterStore, useCounterStore] = createInjectionState((initialValue: number) => {
  // 状态
  const count = shallowRef(initialValue)

  // getters
  const double = computed(() => count.value * 2)

  // actions
  function increment() {
    count.value++
  }

  return { count, double, increment }
}, { defaultValue: 0 })
```

## 类型声明

```ts
export type CreateInjectionStateReturn<
  Arguments extends Array<any>,
  Return,
> = Readonly<
  [
    /**
     * 在提供者组件中调用此函数以创建和提供状态。
     *
     * @param args 传递给可组合函数的参数
     * @returns 可组合函数返回的状态
     */
    useProvidingState: (...args: Arguments) => Return,
    /**
     * 在消费者组件中调用此函数以注入状态。
     *
     * @returns 注入的状态,如果未提供且未设置默认值,则为 `undefined`。
     */
    useInjectedState: () => Return | undefined,
  ]
>
export interface CreateInjectionStateOptions<Return> {
  /**
   * InjectionState 的自定义 injectionKey
   */
  injectionKey?: string | InjectionKey<Return>
  /**
   * InjectionState 的默认值
   */
  defaultValue?: Return
}
/**
 * 创建可以注入到组件中的全局状态。
 *
 * @see https://vueuse.org/createInjectionState
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function createInjectionState<
  Arguments extends Array<any>,
  Return,
>(
  composable: (...args: Arguments) => Return,
  options?: CreateInjectionStateOptions<Return>,
): CreateInjectionStateReturn<Arguments, Return>
```
