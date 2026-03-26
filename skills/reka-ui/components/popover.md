# 弹出框

浮动内容面板

**部分：** `PopoverRoot`、`PopoverTrigger`、`PopoverPortal`、`PopoverContent`、`PopoverArrow`、`PopoverClose`、`PopoverAnchor`

## PopoverRoot

### 属性

| 属性          | 类型      | 默认值 |
| ------------- | --------- | ------- |
| `defaultOpen` | `boolean` | `false` |
| `modal`       | `boolean` | `false` |
| `open`        | `boolean` | -       |

### 事件

| 事件         | 载荷            |
| ------------- | ------------------ |
| `update:open` | `[value: boolean]` |

### 插槽

| 插槽   | 类型      |
| ------ | --------- |
| `open` | `boolean` |

## PopoverTrigger

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## PopoverPortal

### 属性

| 属性         | 类型                    | 默认值 |
| ------------ | ----------------------- | ------- |
| `disabled`   | `boolean`               | -       |
| `forceMount` | `boolean`               | -       |
| `to`         | `string \| HTMLElement` | -       |

## PopoverContent

### 属性

| 属性                          | 类型                                                    | 默认值 |
| ----------------------------- | ------------------------------------------------------- | ------- |
| `align`                       | `"start" \| "center" \| "end"`                          | -       |
| `alignOffset`                 | `number`                                                | -       |
| `arrowPadding`                | `number`                                                | -       |
| `as`                          | `AsTag \| Component`                                    | `"div"` |
| `asChild`                     | `boolean`                                               | -       |
| `avoidCollisions`             | `boolean`                                               | -       |
| `collisionBoundary`           | `Element \| (Element \| null)[] \| null`                | -       |
| `collisionPadding`            | `number \| Partial<Record<"top" \| "right" \| "bott...` | -       |
| `disableOutsidePointerEvents` | `boolean`                                               | -       |
| `forceMount`                  | `boolean`                                               | -       |
| `hideWhenDetached`            | `boolean`                                               | -       |
| `prioritizePosition`          | `boolean`                                               | -       |
| `side`                        | `"top" \| "right" \| "bottom" \| "left"`                | -       |
| `sideOffset`                  | `number`                                                | -       |
| `sticky`                      | `"partial" \| "always"`                                 | -       |
| `trapFocus`                   | `boolean`                                               | -       |
| `updatePositionStrategy`      | `"always" \| "optimized"`                               | -       |

### 事件

| 事件                | 载荷                                               |
| -------------------- | ----------------------------------------------------- |
| `closeAutoFocus`     | `[event: Event]`                                      |
| `escapeKeyDown`      | `[event: KeyboardEvent]`                              |
| `focusOutside`       | `[event: FocusOutsideEvent]`                          |
| `interactOutside`    | `[event: PointerDownOutsideEvent \| FocusOutsideE...` |
| `openAutoFocus`      | `[event: Event]`                                      |
| `pointerDownOutside` | `[event: PointerDownOutsideEvent]`                    |

## PopoverArrow

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"svg"` |
| `asChild` | `boolean`            | -       |
| `height`  | `number`             | `5`     |
| `width`   | `number`             | `10`    |

## PopoverClose

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## PopoverAnchor

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `element` | `Measurable`         | -       |
