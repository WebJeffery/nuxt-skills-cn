# 组合框

带过滤的可搜索下拉菜单

**部分：** `ComboboxRoot`、`ComboboxInput`、`ComboboxAnchor`、`ComboboxTrigger`、`ComboboxContent`、`ComboboxViewport`、`ComboboxItem`、`ComboboxItemIndicator`、`ComboboxGroup`、`ComboboxLabel`、`ComboboxEmpty`、`ComboboxSeparator`、`ComboboxArrow`、`ComboboxPortal`、`ComboboxCancel`、`ComboboxVirtualizer`

## ComboboxRoot

### 属性

| 属性                      | 类型                                                     | 默认值 |
| ------------------------- | -------------------------------------------------------- | ------- |
| `as`                      | `AsTag \| Component`                                     | `"div"` |
| `asChild`                 | `boolean`                                                | -       |
| `defaultOpen`             | `boolean`                                                | -       |
| `defaultValue`            | `AcceptableValue \| AcceptableValue[]`                   | -       |
| `dir`                     | `"ltr" \| "rtl"`                                         | -       |
| `disabled`                | `boolean`                                                | -       |
| `displayValue`            | `((val: AcceptableValue) => string)`                     | -       |
| `filterFunction`          | `((val: string[] \| number[] \| false[] \| true[] \|...` | -       |
| `modelValue`              | `AcceptableValue \| AcceptableValue[]`                   | -       |
| `multiple`                | `boolean`                                                | -       |
| `name`                    | `string`                                                 | -       |
| `open`                    | `boolean`                                                | -       |
| `resetSearchTermOnBlur`   | `boolean`                                                | `true`  |
| `resetSearchTermOnSelect` | `boolean`                                                | `true`  |
| `searchTerm`              | `string`                                                 | -       |
| `selectedValue`           | `AcceptableValue`                                        | -       |

### 事件

| 事件                  | 载荷                    |
| ---------------------- | -------------------------- |
| `update:modelValue`    | `[value: AcceptableValue]` |
| `update:open`          | `[value: boolean]`         |
| `update:searchTerm`    | `[value: string]`          |
| `update:selectedValue` | `[value: AcceptableValue]` |

### 插槽

| 插槽         | 类型                                   |
| ------------ | -------------------------------------- |
| `open`       | `boolean`                              |
| `modelValue` | `AcceptableValue \| AcceptableValue[]` |

## ComboboxInput

### 属性

| 属性        | 类型                 | 默认值   |
| ----------- | -------------------- | --------- |
| `as`        | `AsTag \| Component` | `"input"` |
| `asChild`   | `boolean`            | -         |
| `autoFocus` | `boolean`            | -         |
| `disabled`  | `boolean`            | -         |
| `type`      | `string`             | `"text"`  |

## ComboboxAnchor

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ComboboxTrigger

### 属性

| 属性       | 类型                 | 默认值    |
| ---------- | -------------------- | ---------- |
| `as`       | `AsTag \| Component` | `"button"` |
| `asChild`  | `boolean`            | -          |
| `disabled` | `boolean`            | -          |

## ComboboxContent

### 属性

| 属性                          | 类型                                                    | 默认值 |
| ----------------------------- | ------------------------------------------------------- | ------- |
| `align`                       | `"start" \| "center" \| "end"`                          | -       |
| `alignOffset`                 | `number`                                                | -       |
| `arrowPadding`                | `number`                                                | -       |
| `as`                          | `AsTag \| Component`                                    | `"div"` |
| `asChild`                     | `boolean`                                               | -       |
| `avoidCollisions`             | `boolean`                                               | -       |
| `bodyLock`                    | `boolean`                                               | -       |
| `collisionBoundary`           | `Element \| (Element \| null)[] \| null`                | -       |
| `collisionPadding`            | `number \| Partial<Record<"top" \| "right" \| "bott...` | -       |
| `disableOutsidePointerEvents` | `boolean`                                               | -       |
| `dismissable`                 | `boolean`                                               | -       |
| `forceMount`                  | `boolean`                                               | -       |
| `hideWhenDetached`            | `boolean`                                               | -       |
| `position`                    | `"inline" \| "popper"`                                  | -       |
| `prioritizePosition`          | `boolean`                                               | -       |
| `side`                        | `"top" \| "right" \| "bottom" \| "left"`                | -       |
| `sideOffset`                  | `number`                                                | -       |
| `sticky`                      | `"partial" \| "always"`                                 | -       |
| `updatePositionStrategy`      | `"always" \| "optimized"`                               | -       |

### 事件

| 事件                | 载荷                                               |
| -------------------- | ----------------------------------------------------- |
| `escapeKeyDown`      | `[event: KeyboardEvent]`                              |
| `focusOutside`       | `[event: FocusOutsideEvent]`                          |
| `interactOutside`    | `[event: PointerDownOutsideEvent \| FocusOutsideE...` |
| `pointerDownOutside` | `[event: PointerDownOutsideEvent]`                    |

## ComboboxViewport

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `nonce`   | `string`             | -       |

## ComboboxItem

### 属性

| 属性       | 类型                 | 默认值 |
| ---------- | -------------------- | ------- |
| `as`       | `AsTag \| Component` | `"div"` |
| `asChild`  | `boolean`            | -       |
| `disabled` | `boolean`            | -       |
| `value`\*  | `AcceptableValue`    | -       |

### 事件

| 事件    | 载荷                                 |
| -------- | --------------------------------------- |
| `select` | `[event: SelectEvent<AcceptableValue>]` |

## ComboboxItemIndicator

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |

## ComboboxGroup

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ComboboxLabel

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `for`     | `string`             | -       |

## ComboboxEmpty

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ComboboxSeparator

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ComboboxArrow

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"svg"` |
| `asChild` | `boolean`            | -       |
| `height`  | `number`             | `5`     |
| `width`   | `number`             | `10`    |

## ComboboxPortal

### 属性

| 属性         | 类型                    | 默认值 |
| ------------ | ----------------------- | ------- |
| `disabled`   | `boolean`               | -       |
| `forceMount` | `boolean`               | -       |
| `to`         | `string \| HTMLElement` | -       |

## ComboboxCancel

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |
