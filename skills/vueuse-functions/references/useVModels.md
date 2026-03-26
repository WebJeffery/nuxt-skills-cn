---
category: Component
related: useVModel
---

# useVModels

props v-model 绑定的简写。可以将其视为 `toRefs(props)`，但更改也会触发 emit。

## 用法

```ts
import { useVModels } from '@vueuse/core'

const props = defineProps({
  foo: string,
  bar: number,
})

const emit = defineEmits(['update:foo', 'update:bar'])

const { foo, bar } = useVModels(props, emit)
```

### Options API

```ts
import { useVModels } from '@vueuse/core'

export default {
  props: {
    foo: String,
    bar: Number,
  },
  setup(props, { emit }) {
    const { foo, bar } = useVModels(props, emit)

    console.log(foo.value) // props.foo
    foo.value = 'foo' // emit('update:foo', 'foo')
  },
}
```

## 类型声明

```ts
/**
 * props v-model 绑定的简写。可以视为 `toRefs(props)`，但更改也会触发 emit。
 *
 * @see https://vueuse.org/useVModels
 * @param props
 * @param emit
 * @param options
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useVModels<P extends object, Name extends string>(
  props: P,
  emit?: (name: Name, ...args: any[]) => void,
  options?: UseVModelOptions<any, true>,
): ToRefs<P>
export declare function useVModels<P extends object, Name extends string>(
  props: P,
  emit?: (name: Name, ...args: any[]) => void,
  options?: UseVModelOptions<any, false>,
): ToRefs<P>
```
