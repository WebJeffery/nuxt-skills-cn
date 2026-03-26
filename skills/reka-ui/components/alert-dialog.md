# 警告对话框

需要操作的模态对话框

**部分：** `AlertDialogRoot`、`AlertDialogTrigger`、`AlertDialogPortal`、`AlertDialogOverlay`、`AlertDialogContent`、`AlertDialogTitle`、`AlertDialogDescription`、`AlertDialogCancel`、`AlertDialogAction`

## AlertDialogRoot

### 属性

| 属性          | 类型      | 默认值 |
| ------------- | --------- | ------- |
| `defaultOpen` | `boolean` | -       |
| `open`        | `boolean` | -       |

### 事件

| 事件         | 载荷            |
| ------------- | ------------------ |
| `update:open` | `[value: boolean]` |

## AlertDialogTrigger

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## AlertDialogPortal

### 属性

| 属性         | 类型                    | 默认值 |
| ------------ | ----------------------- | ------- |
| `disabled`   | `boolean`               | -       |
| `forceMount` | `boolean`               | -       |
| `to`         | `string \| HTMLElement` | -       |

## AlertDialogOverlay

### 属性

| 属性         | 类型                 | 默认值 |
| ------------ | -------------------- | ------- |
| `as`         | `AsTag \| Component` | `"div"` |
| `asChild`    | `boolean`            | -       |
| `forceMount` | `boolean`            | -       |

## AlertDialogContent

### 属性

| 属性                          | 类型                 | 默认值 |
| ----------------------------- | -------------------- | ------- |
| `as`                          | `AsTag \| Component` | `"div"` |
| `asChild`                     | `boolean`            | -       |
| `disableOutsidePointerEvents` | `boolean`            | -       |
| `forceMount`                  | `boolean`            | -       |
| `trapFocus`                   | `boolean`            | -       |

### 事件

| 事件                | 载荷                                               |
| -------------------- | ----------------------------------------------------- |
| `closeAutoFocus`     | `[event: Event]`                                      |
| `escapeKeyDown`      | `[event: KeyboardEvent]`                              |
| `focusOutside`       | `[event: FocusOutsideEvent]`                          |
| `interactOutside`    | `[event: PointerDownOutsideEvent \| FocusOutsideE...` |
| `openAutoFocus`      | `[event: Event]`                                      |
| `pointerDownOutside` | `[event: PointerDownOutsideEvent]`                    |

## AlertDialogTitle

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"h2"`  |
| `asChild` | `boolean`            | -       |

## AlertDialogDescription

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"p"`   |
| `asChild` | `boolean`            | -       |

## AlertDialogCancel

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## AlertDialogAction

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |
