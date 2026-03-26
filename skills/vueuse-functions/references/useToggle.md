---
category: Utilities
---

# useToggle

切换布尔值或两个值之间切换的实用函数。

## 用法

```ts
import { useToggle } from '@vueuse/core'

const [value, toggle] = useToggle(true)

value.value // true

toggle()
value.value // false

toggle(true)
value.value // true
```

### 在两个值之间切换

```ts
import { useToggle } from '@vueuse/core'

const [value, toggle] = useToggle('Yes', 'No')

value.value // 'Yes'

toggle()
value.value // 'No'

toggle('Yes')
value.value // 'Yes'
```

## 类型声明

```ts
/**
 * 切换布尔值或两个值之间切换的实用函数。
 *
 * @see https://vueuse.org/useToggle
 * @param [initialValue] - 初始值
 * @param [falseValue] - 当初始值为真值时使用的值
 *
 * @__NO_SIDE_EFFECTS__
 */
export declare function useToggle<T = boolean>(
  initialValue?: T,
  falseValue?: T,
): [Ref<T>, (value?: T) => T]
```
