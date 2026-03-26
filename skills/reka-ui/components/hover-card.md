# 悬停卡片

悬停时显示的卡片

**部分：** `HoverCardRoot`、`HoverCardTrigger`、`HoverCardPortal`、`HoverCardContent`、`HoverCardArrow`

## HoverCardRoot

### 属性

| 属性          | 类型      | 默认值 |
| ------------- | --------- | ------- |
| `closeDelay`  | `number`  | `300`   |
| `defaultOpen` | `false`   | `false` |
| `open`        | `boolean` | -       |
| `openDelay`   | `number`  | `700`   |

### 事件

| 事件         | 载荷            |
| ------------- | ------------------ |
| `update:open` | `[value: boolean]` |

### 插槽

| 插槽   | 类型      |
| ------ | --------- |
| `open` | `boolean` |

## HoverCardTrigger

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"a"`   |
| `asChild` | `boolean`            | -       |

## HoverCardPortal

### 属性

| 属性         | 类型                    | 默认值 |
| ------------ | ----------------------- | ------- |
| `disabled`   | `boolean`               | -       |
| `forceMount` | `boolean`               | -       |
| `to`         | `string \| HTMLElement` | -       |

## HoverCardContent

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
| `prioritizePosition`     | `boolean`                                               | -       |
| `side`                   | `"top" \| "right" \| "bottom" \| "left"`                | -       |
| `sideOffset`             | `number`                                                | -       |
| `sticky`                 | `"partial" \| "always"`                                 | -       |
| `updatePositionStrategy` | `"always" \| "optimized"`                               | -       |

### 事件

| 事件                | 载荷                                               |
| -------------------- | ----------------------------------------------------- |
| `escapeKeyDown`      | `[event: KeyboardEvent]`                              |
| `focusOutside`       | `[event: FocusOutsideEvent]`                          |
| `interactOutside`    | `[event: PointerDownOutsideEvent \| FocusOutsideE...` |
| `pointerDownOutside` | `[event: PointerDownOutsideEvent]`                    |

## HoverCardArrow

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"svg"` |
| `asChild` | `boolean`            | -       |
| `height`  | `number`             | `5`     |
| `width`   | `number`             | `10`    |
