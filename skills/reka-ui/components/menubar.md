# 菜单栏

水平菜单栏

**部分：** `MenubarRoot`、`MenubarMenu`、`MenubarTrigger`、`MenubarPortal`、`MenubarContent`、`MenubarItem`、`MenubarCheckboxItem`、`MenubarRadioGroup`、`MenubarRadioItem`、`MenubarItemIndicator`、`MenubarLabel`、`MenubarGroup`、`MenubarSeparator`、`MenubarSub`、`MenubarSubTrigger`、`MenubarSubContent`、`MenubarArrow`

## MenubarRoot

### 属性

| 属性           | 类型             | 默认值 |
| -------------- | ---------------- | ------- |
| `defaultValue` | `string`         | -       |
| `dir`          | `"ltr" \| "rtl"` | -       |
| `loop`         | `boolean`        | `false` |
| `modelValue`   | `string`         | -       |

### 事件

| 事件               | 载荷            |
| ------------------- | ------------------ |
| `update:modelValue` | `[value: boolean]` |

### 插槽

| 插槽         | 类型     |
| ------------ | -------- |
| `modelValue` | `string` |

## MenubarMenu

### 属性

| 属性    | 类型     | 默认值 |
| ------- | -------- | ------- |
| `value` | `string` | -       |

## MenubarTrigger

### 属性

| 属性       | 类型                 | 默认值    |
| ---------- | -------------------- | ---------- |
| `as`       | `AsTag \| Component` | `"button"` |
| `asChild`  | `boolean`            | -          |
| `disabled` | `boolean`            | -          |

## MenubarPortal

### 属性

| 属性         | 类型                    | 默认值 |
| ------------ | ----------------------- | ------- |
| `disabled`   | `boolean`               | -       |
| `forceMount` | `boolean`               | -       |
| `to`         | `string \| HTMLElement` | -       |

## MenubarContent

### 属性

| 属性                     | 类型                                                    | 默认值   |
| ------------------------ | ------------------------------------------------------- | --------- |
| `align`                  | `"start" \| "center" \| "end"`                          | `"start"` |
| `alignOffset`            | `number`                                                | -         |
| `arrowPadding`           | `number`                                                | -         |
| `as`                     | `AsTag \| Component`                                    | `"div"`   |
| `asChild`                | `boolean`                                               | -         |
| `avoidCollisions`        | `boolean`                                               | -         |
| `collisionBoundary`      | `Element \| (Element \| null)[] \| null`                | -         |
| `collisionPadding`       | `number \| Partial<Record<"top" \| "right" \| "bott...` | -         |
| `forceMount`             | `boolean`                                               | -         |
| `hideWhenDetached`       | `boolean`                                               | -         |
| `loop`                   | `boolean`                                               | -         |
| `prioritizePosition`     | `boolean`                                               | -         |
| `side`                   | `"top" \| "right" \| "bottom" \| "left"`                | -         |
| `sideOffset`             | `number`                                                | -         |
| `sticky`                 | `"partial" \| "always"`                                 | -         |
| `updatePositionStrategy` | `"always" \| "optimized"`                               | -         |

### 事件

| 事件                | 载荷                                               |
| -------------------- | ----------------------------------------------------- |
| `closeAutoFocus`     | `[event: Event]`                                      |
| `escapeKeyDown`      | `[event: KeyboardEvent]`                              |
| `focusOutside`       | `[event: FocusOutsideEvent]`                          |
| `interactOutside`    | `[event: PointerDownOutsideEvent \| FocusOutsideE...` |
| `pointerDownOutside` | `[event: PointerDownOutsideEvent]`                    |

## MenubarItem

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

## MenubarCheckboxItem

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

## MenubarRadioGroup

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

## MenubarRadioItem

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

## MenubarItemIndicator

### 属性

| 属性         | 类型                 | 默认值 |
| ------------ | -------------------- | ------- |
| `as`         | `AsTag \| Component` | `"div"` |
| `asChild`    | `boolean`            | -       |
| `forceMount` | `boolean`            | -       |

## MenubarLabel

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## MenubarGroup

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## MenubarSeparator

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## MenubarSub

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

## MenubarSubTrigger

### 属性

| 属性        | 类型                 | 默认值 |
| ----------- | -------------------- | ------- |
| `as`        | `AsTag \| Component` | `"div"` |
| `asChild`   | `boolean`            | -       |
| `disabled`  | `boolean`            | -       |
| `textValue` | `string`             | -       |

## MenubarSubContent

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

## MenubarArrow

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"svg"` |
| `asChild` | `boolean`            | -       |
| `height`  | `number`             | `5`     |
| `width`   | `number`             | `10`    |
