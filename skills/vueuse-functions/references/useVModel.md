---
category: Component
---

# useVModel

v-model 绑定的简写，props + emit -> ref

> 我们鼓励您在此组合式函数上使用 Vue 的 [`defineModel`](https://vuejs.org/api/sfc-script-setup.html#definemodel)，但是在使用 `TSX` 或 `deep: true` 选项等一些边缘情况下，`defineModel` 不支持。

## 用法

```ts
import { useVModel } from '@vueuse/core'

const props = defineProps<{
  modelValue: string
}>()
const emit = defineEmits(['update:modelValue'])

const data = useVModel(props, 'modelValue', emit)
```

### Options API

```ts
import { useVModel } from '@vueuse/core'

export default {
  setup(props, { emit }) {
    const data = useVModel(props, 'data', emit)

    console.log(data.value) // props.data
    data.value = 'foo' // emit('update:data', 'foo')
  },
}
```

## 选项

### Passive Mode

默认情况下，`useVModel` 返回一个计算 ref。在被动模式下，它创建一个本地 ref，通过 `watch` 与 prop 同步，允许深度响应式跟踪。

```ts
const data = useVModel(props, 'modelValue', emit, { passive: true })
```

### Deep Watching

使用 `passive: true` 时，可以为嵌套对象更改启用深度监视：

```ts
const data = useVModel(props, 'modelValue', emit, {
  passive: true,
  deep: true,
})
```

### Clone Values

克隆 prop 值以避免变异原始对象。设置为 `true` 以使用 `JSON.parse(JSON.stringify())` 或提供自定义克隆函数。

```ts
const data = useVModel(props, 'modelValue', emit, {
  clone: true,
  // 或提供自定义克隆函数
  // clone: (val) => structuredClone(val),
})
```

### Default Value

当 prop 未定义时提供默认值：

```ts
const data = useVModel(props, 'modelValue', emit, {
  defaultValue: 'default',
})
```

### Custom Event Name

覆盖默认的 `update:propName` 事件名称：

```ts
const data = useVModel(props, 'value', emit, {
  eventName: 'change',
})
```

### Emit Validation

使用 `shouldEmit` 在发出之前进行验证。返回 `false` 以防止发出：

```ts
const data = useVModel(props, 'modelValue', emit, {
  shouldEmit: (value) => {
    // 仅在值有效时发出
    return value.length > 0
  },
})
```

## 类型声明

```ts
export interface UseVModelOptions<T, Passive extends boolean = false> {
  /**
   * 当 passive 设置为 `true` 时，它将使用 `watch` 与 props 和 ref 同步。
   * 而不是依赖 `v-model` 或 `.sync` 来工作。
   *
   * @default false
   */
  passive?: Passive
  /**
   * 当设置 eventName 时，其值将用于覆盖 emit 事件名称。
   *
   * @default undefined
   */
  eventName?: string
  /**
   * 尝试检查深度嵌套对象或数组中的属性更改。
   * 仅在设置 `passive` 选项为 `true` 时应用
   *
   * @default false
   */
  deep?: boolean
  /**
   * 为返回的 ref 定义默认值，当没有传递值时。
   *
   * @default undefined
   */
  defaultValue?: T
  /**
   * 克隆 props。
   * 接受自定义克隆函数。
   * 当设置为 `true` 时，它将使用 `JSON.parse(JSON.stringify(value))` 来克隆。
   *
   * @default false
   */
  clone?: boolean | CloneFn<T>
  /**
   * 在触发 emit 事件之前的钩子可用于表单验证。
   * 如果返回 false，则不会触发 emit 事件。
   *
   * @default undefined
   */
  shouldEmit?: (v: T) => boolean
}
/**
 * v-model 绑定的简写，props + emit -> ref
 *
 * @see https://vueuse.org/useVModel
 * @param props
 * @param key (default 'modelValue')
 * @param emit
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useVModel<
  P extends object,
  K extends keyof P,
  Name extends string,
>(
  props: P,
  key?: K,
  emit?: (name: Name, ...args: any[]) => void,
  options?: UseVModelOptions<P[K], false>,
): WritableComputedRef<P[K]>
export declare function useVModel<
  P extends object,
  K extends keyof P,
  Name extends string,
>(
  props: P,
  key?: K,
  emit?: (name: Name, ...args: any[]) => void,
  options?: UseVModelOptions<P[K], true>,
): Ref<UnwrapRef<P[K]>>
```
