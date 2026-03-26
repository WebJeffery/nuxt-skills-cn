---
category: Sensors
---

# useMagicKeys

响应式按键按下状态，支持魔法键组合。

## 用法

```ts
import { useMagicKeys } from '@vueuse/core'

const { shift, space, a /* 您想要监视的键 */ } = useMagicKeys()

watch(space, (v) => {
  if (v)
    console.log('空格键已被按下')
})

watchEffect(() => {
  if (shift.value && a.value)
    console.log('Shift + A 已被按下')
})
```

::: tip 注意
如果您在 `tsconfig.json` 中启用了 `noUncheckedIndexedAccess` 的 TypeScript（或使用默认启用它的 Nuxt），解构的键将具有类型 `ComputedRef<boolean> | undefined`。

`noUncheckedIndexedAccess` TypeScript 选项将 `undefined` 添加到通过索引签名访问的任何未声明字段。由于 `useMagicKeys()` 使用索引签名以允许动态访问任何键，TypeScript 出于类型安全考虑，会将解构属性视为可能未定义。

您需要使用可选链或用 getter 函数包装：

```ts
const { shift, space, a } = useMagicKeys()

watch(
  () => space?.value,
  (v) => {
    if (v)
      console.log('空格键已被按下')
  },
)

watchEffect(() => {
  if (shift?.value && a?.value)
    console.log('Shift + A 已被按下')
})
```

请查看 [TypeScript 文档](https://www.typescriptlang.org/tsconfig/#noUncheckedIndexedAccess) 以获取有关 `noUncheckedIndexedAccess` 的更多详细信息。

:::

查看 [所有可能的键码](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/code/code_values)。

### 组合

您可以通过用 `+` 或 `_` 连接键来神奇地使用组合（快捷键/热键）。

```ts
import { useMagicKeys } from '@vueuse/core'

const keys = useMagicKeys()
const shiftCtrlA = keys['Shift+Ctrl+A']

watch(shiftCtrlA, (v) => {
  if (v)
    console.log('Shift + Ctrl + A 已被按下')
})
```

```ts
import { useMagicKeys } from '@vueuse/core'

const { Ctrl_A_B, space, alt_s /* ... */ } = useMagicKeys()

watch(Ctrl_A_B, (v) => {
  if (v)
    console.log('Control+A+B 已被按下')
})
```

您还可以使用 `whenever` 函数使其更短

```ts
import { useMagicKeys, whenever } from '@vueuse/core'

const keys = useMagicKeys()

whenever(keys.shift_space, () => {
  console.log('Shift+Space 已被按下')
})
```

### 当前按下的键

提供了一个特殊属性 `current` 来表示当前按下的所有键。

```ts
import { useMagicKeys, whenever } from '@vueuse/core'

const { current } = useMagicKeys()

console.log(current) // Set { 'control', 'a' }

whenever(
  () => current.has('a') && !current.has('b'),
  () => console.log('A 被按下但 B 没有'),
)
```

### 键别名

```ts
import { useMagicKeys, whenever } from '@vueuse/core'

const { shift_cool } = useMagicKeys({
  aliasMap: {
    cool: 'space',
  },
})

whenever(shift_cool, () => console.log('Shift + Space 已被按下'))
```

默认情况下，我们有一些[常见实践的预配置别名](https://github.com/vueuse/vueuse/blob/main/packages/core/useMagicKeys/aliasMap.ts)。

### 有条件地禁用

您的应用程序中可能有一些 `<input />` 元素，并且您不希望在用户专注于这些输入时触发魔法键处理。这是使用 `useActiveElement` 和 `logicAnd` 来执行此操作的示例。

```ts
import { useActiveElement, useMagicKeys, whenever } from '@vueuse/core'
import { logicAnd } from '@vueuse/math'

const activeElement = useActiveElement()
const notUsingInput = computed(() =>
  activeElement.value?.tagName !== 'INPUT'
  && activeElement.value?.tagName !== 'TEXTAREA',)

const { tab } = useMagicKeys()

whenever(logicAnd(tab, notUsingInput), () => {
  console.log('在输入之外按下了 Tab 键！')
})
```

### 自定义事件处理程序

```ts
import { useMagicKeys, whenever } from '@vueuse/core'

const { ctrl_s } = useMagicKeys({
  passive: false,
  onEventFired(e) {
    if (e.ctrlKey && e.key === 's' && e.type === 'keydown')
      e.preventDefault()
  },
})

whenever(ctrl_s, () => console.log('Ctrl+S 已被按下'))
```

> ⚠️ 不推荐这种用法，请谨慎使用。

### 响应式模式

默认情况下，`useMagicKeys()` 的值是 `Ref<boolean>`。如果您想在模板中使用对象，可以将其设置为响应式模式。

```ts
import { useMagicKeys } from '@vueuse/core'
// ---cut---
const keys = useMagicKeys({ reactive: true })
```

```vue
<template>
  <div v-if="keys.shift">
    您正在按住 Shift 键！
  </div>
</template>
```

## Type Declarations

```ts
export interface UseMagicKeysOptions<Reactive extends boolean> {
  /**
   * Returns a reactive object instead of an object of refs
   *
   * @default false
   */
  reactive?: Reactive
  /**
   * Target for listening events
   *
   * @default window
   */
  target?: MaybeRefOrGetter<EventTarget>
  /**
   * Alias map for keys, all the keys should be lowercase
   * { target: keycode }
   *
   * @example { ctrl: "control" }
   * @default <predefined-map>
   */
  aliasMap?: Record<string, string>
  /**
   * Register passive listener
   *
   * @default true
   */
  passive?: boolean
  /**
   * Custom event handler for keydown/keyup event.
   * Useful when you want to apply custom logic.
   *
   * When using `e.preventDefault()`, you will need to pass `passive: false` to useMagicKeys().
   */
  onEventFired?: (e: KeyboardEvent) => void | boolean
}
export interface MagicKeysInternal {
  /**
   * A Set of currently pressed keys,
   * Stores raw keyCodes.
   *
   * @see https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key
   */
  current: Set<string>
}
export type UseMagicKeysReturn<Reactive extends boolean> = Readonly<
  Record<string, Reactive extends true ? boolean : ComputedRef<boolean>> &
    MagicKeysInternal
>
/**
 * Reactive keys pressed state, with magical keys combination support.
 *
 * @see https://vueuse.org/useMagicKeys
 */
export declare function useMagicKeys<T extends boolean = false>(
  options?: UseMagicKeysOptions<T>,
): UseMagicKeysReturn<T>
export { DefaultMagicKeysAliasMap } from "./aliasMap"
```
