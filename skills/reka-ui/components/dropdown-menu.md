# 下拉菜单

下拉操作菜单

**部分：** `DropdownMenuRoot`、`DropdownMenuTrigger`、`DropdownMenuPortal`、`DropdownMenuContent`、`DropdownMenuItem`、`DropdownMenuCheckboxItem`、`DropdownMenuRadioGroup`、`DropdownMenuRadioItem`、`DropdownMenuItemIndicator`、`DropdownMenuLabel`、`DropdownMenuGroup`、`DropdownMenuSeparator`、`DropdownMenuSub`、`DropdownMenuSubTrigger`、`DropdownMenuSubContent`、`DropdownMenuArrow`

## DropdownMenuRoot

### 属性

| 属性          | 类型             | 默认值 |
| ------------- | ---------------- | ------- |
| `defaultOpen` | `boolean`        | -       |
| `dir`         | `"ltr" \| "rtl"` | -       |
| `modal`       | `boolean`        | `true`  |
| `open`        | `boolean`        | -       |

### 事件

| 事件         | 载荷              |
| ------------- | -------------------- |
| `update:open` | `[payload: boolean]` |

### 插槽

| 插槽   | 类型      |
| ------ | --------- |
| `open` | `boolean` |

## DropdownMenuTrigger

### 属性

| 属性       | 类型                 | 默认值    |
| ---------- | -------------------- | ---------- |
| `as`       | `AsTag \| Component` | `"button"` |
| `asChild`  | `boolean`            | -          |
| `disabled` | `boolean`            | -          |

## DropdownMenuPortal

### 属性

| 属性         | 类型                    | 默认值 |
| ------------ | ----------------------- | ------- |
| `disabled`   | `boolean`               | -       |
| `forceMount` | `boolean`               | -       |
| `to`         | `string \| HTMLElement` | -       |

## DropdownMenuContent

### 属性

| 属性                     | 类型                                                    | 默认值 |
| ------------------------ | ------------------------------------------------------- | ------- |
| `align`                  | `"start" \| "center" \| "end"`                          | -       |
| `alignOffset`            | `number`                                                | -       |
| `arrowPadding`           | `number`                                                | -       |
| `as`                     | `AsTag \| Component`                                    | `"div"` |
| `asChild`                | `boolean`                                               | -       |
| `avoidCollisions`        | `boolean`                                               | -       |
| `collisionBoundary`      | `Element \| (Element \| null)[] \| null`                | -       |
| `collisionPadding`       | `number \| Partial<Record<"top" \| "right" \| "bott...` | -       |
| `forceMount`             | `boolean`                                               | -       |
| `hideWhenDetached`       | `boolean`                                               | -       |
| `loop`                   | `boolean`                                               | -       |
| `prioritizePosition`     | `boolean`                                               | -       |
| `side`                   | `"top" \| "right" \| "bottom" \| "left"`                | -       |
| `sideOffset`             | `number`                                                | -       |
| `sticky`                 | `"partial" \| "always"`                                 | -       |
| `updatePositionStrategy` | `"always" \| "optimized"`                               | -       |

### 事件

| 事件                | 载荷                                               |
| -------------------- | ----------------------------------------------------- |
| `closeAutoFocus`     | `[event: Event]`                                      |
| `escapeKeyDown`      | `[event: KeyboardEvent]`                              |
| `focusOutside`       | `[event: FocusOutsideEvent]`                          |
| `interactOutside`    | `[event: PointerDownOutsideEvent \| FocusOutsideE...` |
| `pointerDownOutside` | `[event: PointerDownOutsideEvent]`                    |

## DropdownMenuItem

### 属性

| 属性        | 类型                 | 默认值 |
| ----------- | -------------------- | ------- |
| `as`        | `AsTag \| Component` | `"div"` |
| `asChild`   | `boolean`            | -       |
| `disabled`  | `boolean`            | -       |
| `textValue` | `string`             | -       |

### 事件

| 事件    | 载荷          |
| -------- | ---------------- |
| `select` | `[event: Event]` |

## DropdownMenuCheckboxItem

### 属性

| 属性        | 类型                               | 默认值 |
| ----------- | ---------------------------------- | ------- |
| `as`        | `AsTag \| Component`               | `"div"` |
| `asChild`   | `boolean`                          | -       |
| `checked`   | `false \| true \| "indeterminate"` | -       |
| `disabled`  | `boolean`                          | -       |
| `textValue` | `string`                           | -       |

### 事件

| 事件            | 载荷              |
| ---------------- | -------------------- |
| `select`         | `[event: Event]`     |
| `update:checked` | `[payload: boolean]` |

## DropdownMenuRadioGroup

### 属性

| 属性         | 类型                 | 默认值 |
| ------------ | -------------------- | ------- |
| `as`         | `AsTag \| Component` | `"div"` |
| `asChild`    | `boolean`            | -       |
| `modelValue` | `string`             | -       |

### 事件

| 事件               | 载荷             |
| ------------------- | ------------------- |
| `update:modelValue` | `[payload: string]` |

## DropdownMenuRadioItem

### 属性

| 属性        | 类型                 | 默认值 |
| ----------- | -------------------- | ------- |
| `as`        | `AsTag \| Component` | `"div"` |
| `asChild`   | `boolean`            | -       |
| `disabled`  | `boolean`            | -       |
| `textValue` | `string`             | -       |
| `value`\*   | `string`             | -       |

### 事件

| 事件    | 载荷          |
| -------- | ---------------- |
| `select` | `[event: Event]` |

## DropdownMenuItemIndicator

### 属性

| 属性         | 类型                 | 默认值 |
| ------------ | -------------------- | ------- |
| `as`         | `AsTag \| Component` | `"div"` |
| `asChild`    | `boolean`            | -       |
| `forceMount` | `boolean`            | -       |

## DropdownMenuLabel

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## DropdownMenuGroup

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## DropdownMenuSeparator

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## DropdownMenuSub

### 属性

| 属性          | 类型      | 默认值 |
| ------------- | --------- | ------- |
| `defaultOpen` | `boolean` | -       |
| `open`        | `boolean` | -       |

### 事件

| 事件         | 载荷              |
| ------------- | -------------------- |
| `update:open` | `[payload: boolean]` |

### 插槽

| 插槽   | 类型      |
| ------ | --------- |
| `open` | `boolean` |

## DropdownMenuSubTrigger

### 属性

| 属性        | 类型                 | 默认值 |
| ----------- | -------------------- | ------- |
| `as`        | `AsTag \| Component` | `"div"` |
| `asChild`   | `boolean`            | -       |
| `disabled`  | `boolean`            | -       |
| `textValue` | `string`             | -       |

## DropdownMenuSubContent

### 属性

| 属性                     | 类型                                                    | 默认值 |
| ------------------------ | ------------------------------------------------------- | ------- |
| `alignOffset`            | `number`                                                | -       |
| `arrowPadding`           | `number`                                                | -       |
| `as`                     | `AsTag \| Component`                                    | `"div"` |
| `asChild`                | `boolean`                                               | -       |
| `avoidCollisions`        | `boolean`                                               | -       |
| `collisionBoundary`      | `Element \| (Element \| null)[] \| null`                | -       |
| `collisionPadding`       | `number \| Partial<Record<"top" \| "right" \| "bott...` | -       |
| `forceMount`             | `boolean`                                               | -       |
| `hideWhenDetached`       | `boolean`                                               | -       |
| `loop`                   | `boolean`                                               | -       |
| `prioritizePosition`     | `boolean`                                               | -       |
| `sideOffset`             | `number`                                                | -       |
| `sticky`                 | `"partial" \| "always"`                                 | -       |
| `updatePositionStrategy` | `"always" \| "optimized"`                               | -       |

### 事件

| 事件                | 载荷                                               |
| -------------------- | ----------------------------------------------------- |
| `closeAutoFocus`     | `[event: Event]`                                      |
| `entryFocus`         | `[event: Event]`                                      |
| `escapeKeyDown`      | `[event: KeyboardEvent]`                              |
| `focusOutside`       | `[event: FocusOutsideEvent]`                          |
| `interactOutside`    | `[event: PointerDownOutsideEvent \| FocusOutsideE...` |
| `openAutoFocus`      | `[event: Event]`                                      |
| `pointerDownOutside` | `[event: PointerDownOutsideEvent]`                    |

## DropdownMenuArrow

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"svg"` |
| `asChild` | `boolean`            | -       |
| `height`  | `number`             | `5`     |
| `width`   | `number`             | `10`    |
