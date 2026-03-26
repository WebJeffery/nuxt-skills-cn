# 工具提示

悬停信息提示

**部分：** `TooltipProvider`、`TooltipRoot`、`TooltipTrigger`、`TooltipPortal`、`TooltipContent`、`TooltipArrow`

## TooltipProvider

### 属性

| 属性                      | 类型      | 默认值 |
| ------------------------- | --------- | ------- |
| `delayDuration`           | `number`  | `700`   |
| `disableClosingTrigger`   | `boolean` | -       |
| `disabled`                | `boolean` | -       |
| `disableHoverableContent` | `boolean` | `false` |
| `ignoreNonKeyboardFocus`  | `boolean` | `false` |
| `skipDelayDuration`       | `number`  | `300`   |

## TooltipRoot

### 属性

| 属性                      | 类型      | 默认值 |
| ------------------------- | --------- | ------- |
| `defaultOpen`             | `boolean` | `false` |
| `delayDuration`           | `number`  | -       |
| `disableClosingTrigger`   | `boolean` | -       |
| `disabled`                | `boolean` | -       |
| `disableHoverableContent` | `boolean` | -       |
| `ignoreNonKeyboardFocus`  | `boolean` | -       |
| `open`                    | `boolean` | -       |

### 事件

| 事件         | 载荷            |
| ------------- | ------------------ |
| `update:open` | `[value: boolean]` |

### 插槽

| 插槽   | 类型      |
| ------ | --------- |
| `open` | `boolean` |

## TooltipTrigger

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## TooltipPortal

### 属性

| 属性         | 类型                    | 默认值 |
| ------------ | ----------------------- | ------- |
| `disabled`   | `boolean`               | -       |
| `forceMount` | `boolean`               | -       |
| `to`         | `string \| HTMLElement` | -       |

## TooltipContent

### 属性

| 属性                | 类型                                                    | 默认值 |
| ------------------- | ------------------------------------------------------- | ------- |
| `align`             | `"start" \| "center" \| "end"`                          | -       |
| `alignOffset`       | `number`                                                | -       |
| `ariaLabel`         | `string`                                                | -       |
| `arrowPadding`      | `number`                                                | -       |
| `as`                | `AsTag \| Component`                                    | `"div"` |
| `asChild`           | `boolean`                                               | -       |
| `avoidCollisions`   | `boolean`                                               | -       |
| `collisionBoundary` | `Element \| (Element \| null)[] \| null`                | -       |
| `collisionPadding`  | `number \| Partial<Record<"top" \| "right" \| "bott...` | -       |
| `forceMount`        | `boolean`                                               | -       |
| `hideWhenDetached`  | `boolean`                                               | -       |
| `side`              | `"top" \| "right" \| "bottom" \| "left"`                | `"top"` |
| `sideOffset`        | `number`                                                | -       |
| `sticky`            | `"partial" \| "always"`                                 | -       |

### 事件

| 事件                | 载荷                  |
| -------------------- | ------------------------ |
| `escapeKeyDown`      | `[event: KeyboardEvent]` |
| `pointerDownOutside` | `[event: Event]`         |

## TooltipArrow

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"svg"` |
| `asChild` | `boolean`            | -       |
| `height`  | `number`             | `5`     |
| `width`   | `number`             | `10`    |
