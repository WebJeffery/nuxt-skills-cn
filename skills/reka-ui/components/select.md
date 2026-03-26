# 选择

带有分组的下拉选择

**部分：** `SelectRoot`、`SelectTrigger`、`SelectPortal`、`SelectContent`、`SelectViewport`、`SelectItem`、`SelectItemText`、`SelectItemIndicator`、`SelectGroup`、`SelectLabel`、`SelectSeparator`、`SelectArrow`、`SelectScrollUpButton`、`SelectScrollDownButton`、`SelectValue`、`SelectIcon`

## SelectRoot

### 属性

| 属性           | 类型             | 默认值 |
| -------------- | ---------------- | ------- |
| `autocomplete` | `string`         | -       |
| `defaultOpen`  | `boolean`        | -       |
| `defaultValue` | `string`         | `""`    |
| `dir`          | `"ltr" \| "rtl"` | -       |
| `disabled`     | `boolean`        | -       |
| `modelValue`   | `string`         | -       |
| `name`         | `string`         | -       |
| `open`         | `boolean`        | -       |
| `required`     | `boolean`        | -       |

### 事件

| 事件               | 载荷            |
| ------------------- | ------------------ |
| `update:modelValue` | `[value: string]`  |
| `update:open`       | `[value: boolean]` |

### 插槽

| 插槽         | 类型      |
| ------------ | --------- |
| `modelValue` | `string`  |
| `open`       | `boolean` |

## SelectTrigger

### 属性

| 属性       | 类型                 | 默认值    |
| ---------- | -------------------- | ---------- |
| `as`       | `AsTag \| Component` | `"button"` |
| `asChild`  | `boolean`            | -          |
| `disabled` | `boolean`            | -          |

## SelectPortal

### 属性

| 属性         | 类型                    | 默认值 |
| ------------ | ----------------------- | ------- |
| `disabled`   | `boolean`               | -       |
| `forceMount` | `boolean`               | -       |
| `to`         | `string \| HTMLElement` | -       |

## SelectContent

### 属性

| 属性                     | 类型                                                    | 默认值 |
| ------------------------ | ------------------------------------------------------- | ------- |
| `align`                  | `"start" \| "center" \| "end"`                          | -       |
| `alignOffset`            | `number`                                                | -       |
| `arrowPadding`           | `number`                                                | -       |
| `as`                     | `AsTag \| Component`                                    | `"div"` |
| `asChild`                | `boolean`                                               | -       |
| `avoidCollisions`        | `boolean`                                               | -       |
| `bodyLock`               | `boolean`                                               | -       |
| `collisionBoundary`      | `Element \| (Element \| null)[] \| null`                | -       |
| `collisionPadding`       | `number \| Partial<Record<"top" \| "right" \| "bott...` | -       |
| `forceMount`             | `boolean`                                               | -       |
| `hideWhenDetached`       | `boolean`                                               | -       |
| `position`               | `"popper" \| "item-aligned"`                            | -       |
| `prioritizePosition`     | `boolean`                                               | -       |
| `side`                   | `"top" \| "right" \| "bottom" \| "left"`                | -       |
| `sideOffset`             | `number`                                                | -       |
| `sticky`                 | `"partial" \| "always"`                                 | -       |
| `updatePositionStrategy` | `"always" \| "optimized"`                               | -       |

### 事件

| 事件                | 载荷                            |
| -------------------- | ---------------------------------- |
| `closeAutoFocus`     | `[event: Event]`                   |
| `escapeKeyDown`      | `[event: KeyboardEvent]`           |
| `pointerDownOutside` | `[event: PointerDownOutsideEvent]` |

## SelectViewport

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `nonce`   | `string`             | -       |

## SelectItem

### 属性

| 属性        | 类型                 | 默认值 |
| ----------- | -------------------- | ------- |
| `as`        | `AsTag \| Component` | `"div"` |
| `asChild`   | `boolean`            | -       |
| `disabled`  | `boolean`            | -       |
| `textValue` | `string`             | -       |
| `value`\*   | `string`             | -       |

## SelectItemText

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |

## SelectItemIndicator

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |

## SelectGroup

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## SelectLabel

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `for`     | `string`             | -       |

## SelectSeparator

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## SelectArrow

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"svg"` |
| `asChild` | `boolean`            | -       |
| `height`  | `number`             | `5`     |
| `width`   | `number`             | `10`    |

## SelectScrollUpButton

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## SelectScrollDownButton

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## SelectValue

### 属性

| 属性          | 类型                 | 默认值  |
| ------------- | -------------------- | -------- |
| `as`          | `AsTag \| Component` | `"span"` |
| `asChild`     | `boolean`            | -        |
| `placeholder` | `string`             | `""`     |

## SelectIcon

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |
