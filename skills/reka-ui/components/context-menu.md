# 上下文菜单

右键上下文菜单

**部分：** `ContextMenuRoot`、`ContextMenuTrigger`、`ContextMenuPortal`、`ContextMenuContent`、`ContextMenuItem`、`ContextMenuCheckboxItem`、`ContextMenuRadioGroup`、`ContextMenuRadioItem`、`ContextMenuItemIndicator`、`ContextMenuLabel`、`ContextMenuGroup`、`ContextMenuSeparator`、`ContextMenuSub`、`ContextMenuSubTrigger`、`ContextMenuSubContent`、`ContextMenuArrow`

## ContextMenuRoot

### 属性

| 属性    | 类型             | 默认值 |
| ------- | ---------------- | ------- |
| `dir`   | `"ltr" \| "rtl"` | -       |
| `modal` | `boolean`        | `true`  |

### 事件

| 事件         | 载荷              |
| ------------- | -------------------- |
| `update:open` | `[payload: boolean]` |

## ContextMenuTrigger

### 属性

| 属性       | 类型                 | 默认值  |
| ---------- | -------------------- | -------- |
| `as`       | `AsTag \| Component` | `"span"` |
| `asChild`  | `boolean`            | -        |
| `disabled` | `boolean`            | `false`  |

## ContextMenuPortal

### 属性

| 属性         | 类型                    | 默认值 |
| ------------ | ----------------------- | ------- |
| `disabled`   | `boolean`               | -       |
| `forceMount` | `boolean`               | -       |
| `to`         | `string \| HTMLElement` | -       |

## ContextMenuContent

### 属性

| 属性                 | 类型                                                    | 默认值     |
| -------------------- | ------------------------------------------------------- | ----------- |
| `alignOffset`        | `number`                                                | `0`         |
| `as`                 | `AsTag \| Component`                                    | `"div"`     |
| `asChild`            | `boolean`                                               | -           |
| `avoidCollisions`    | `boolean`                                               | `true`      |
| `collisionBoundary`  | `Element \| (Element \| null)[] \| null`                | `[]`        |
| `collisionPadding`   | `number \| Partial<Record<"top" \| "right" \| "bott...` | `0`         |
| `forceMount`         | `boolean`                                               | -           |
| `hideWhenDetached`   | `boolean`                                               | `false`     |
| `loop`               | `boolean`                                               | -           |
| `prioritizePosition` | `boolean`                                               | -           |
| `sticky`             | `"partial" \| "always"`                                 | `"partial"` |

### 事件

| 事件                | 载荷                                               |
| -------------------- | ----------------------------------------------------- |
| `closeAutoFocus`     | `[event: Event]`                                      |
| `escapeKeyDown`      | `[event: KeyboardEvent]`                              |
| `focusOutside`       | `[event: FocusOutsideEvent]`                          |
| `interactOutside`    | `[event: PointerDownOutsideEvent \| FocusOutsideE...` |
| `pointerDownOutside` | `[event: PointerDownOutsideEvent]`                    |

## ContextMenuItem

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

## ContextMenuCheckboxItem

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

## ContextMenuRadioGroup

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

## ContextMenuRadioItem

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

## ContextMenuItemIndicator

### 属性

| 属性         | 类型                 | 默认值 |
| ------------ | -------------------- | ------- |
| `as`         | `AsTag \| Component` | `"div"` |
| `asChild`    | `boolean`            | -       |
| `forceMount` | `boolean`            | -       |

## ContextMenuLabel

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ContextMenuGroup

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ContextMenuSeparator

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ContextMenuSub

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

## ContextMenuSubTrigger

### 属性

| 属性        | 类型                 | 默认值 |
| ----------- | -------------------- | ------- |
| `as`        | `AsTag \| Component` | `"div"` |
| `asChild`   | `boolean`            | -       |
| `disabled`  | `boolean`            | -       |
| `textValue` | `string`             | -       |

## ContextMenuSubContent

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

## ContextMenuArrow

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"svg"` |
| `asChild` | `boolean`            | -       |
| `height`  | `number`             | `5`     |
| `width`   | `number`             | `10`    |
