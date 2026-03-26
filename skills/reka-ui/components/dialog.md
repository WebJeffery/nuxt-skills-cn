# 对话框

模态对话框

**部分：** `DialogRoot`、`DialogTrigger`、`DialogPortal`、`DialogOverlay`、`DialogContent`、`DialogTitle`、`DialogDescription`、`DialogClose`

## DialogRoot

### 属性

| 属性          | 类型      | 默认值 |
| ------------- | --------- | ------- |
| `defaultOpen` | `boolean` | `false` |
| `modal`       | `boolean` | `true`  |
| `open`        | `boolean` | -       |

### 事件

| 事件         | 载荷            |
| ------------- | ------------------ |
| `update:open` | `[value: boolean]` |

### 插槽

| 插槽   | 类型      |
| ------ | --------- |
| `open` | `boolean` |

## DialogTrigger

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## DialogPortal

### 属性

| 属性         | 类型                    | 默认值 |
| ------------ | ----------------------- | ------- |
| `disabled`   | `boolean`               | -       |
| `forceMount` | `boolean`               | -       |
| `to`         | `string \| HTMLElement` | -       |

## DialogOverlay

### 属性

| 属性         | 类型                 | 默认值 |
| ------------ | -------------------- | ------- |
| `as`         | `AsTag \| Component` | `"div"` |
| `asChild`    | `boolean`            | -       |
| `forceMount` | `boolean`            | -       |

## DialogContent

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

## DialogTitle

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"h2"`  |
| `asChild` | `boolean`            | -       |

## DialogDescription

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"p"`   |
| `asChild` | `boolean`            | -       |

## DialogClose

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |
