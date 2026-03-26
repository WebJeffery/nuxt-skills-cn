# 提示

临时通知

**部分：** `ToastProvider`、`ToastRoot`、`ToastViewport`、`ToastTitle`、`ToastDescription`、`ToastAction`、`ToastClose`、`ToastPortal`

## ToastProvider

### 属性

| 属性             | 类型                                  | 默认值          |
| ---------------- | ------------------------------------- | ---------------- |
| `duration`       | `number`                              | `5000`           |
| `label`          | `string`                              | `"Notification"` |
| `swipeDirection` | `"right" \| "left" \| "up" \| "down"` | `"right"`        |
| `swipeThreshold` | `number`                              | `50`             |

## ToastRoot

### 属性

| 属性          | 类型                           | 默认值        |
| ------------- | ------------------------------ | -------------- |
| `as`          | `AsTag \| Component`           | `"li"`         |
| `asChild`     | `boolean`                      | -              |
| `defaultOpen` | `boolean`                      | `true`         |
| `duration`    | `number`                       | -              |
| `forceMount`  | `boolean`                      | -              |
| `open`        | `boolean`                      | -              |
| `type`        | `"foreground" \| "background"` | `"foreground"` |

### 事件

| 事件           | 载荷                  |
| --------------- | ------------------------ |
| `escapeKeyDown` | `[event: KeyboardEvent]` |
| `pause`         | `[]`                     |
| `resume`        | `[]`                     |
| `swipeCancel`   | `[event: SwipeEvent]`    |
| `swipeEnd`      | `[event: SwipeEvent]`    |
| `swipeMove`     | `[event: SwipeEvent]`    |
| `swipeStart`    | `[event: SwipeEvent]`    |
| `update:open`   | `[value: boolean]`       |

### 插槽

| 插槽        | 类型      |
| ----------- | --------- |
| `open`      | `boolean` |
| `remaining` | `number`  |
| `duration`  | `number`  |

## ToastViewport

### 属性

| 属性      | 类型                                     | 默认值                      |
| --------- | ---------------------------------------- | ---------------------------- |
| `as`      | `AsTag \| Component`                     | `"ol"`                       |
| `asChild` | `boolean`                                | -                            |
| `hotkey`  | `string[]`                               | `["F8"]`                     |
| `label`   | `string \| ((hotkey: string) => string)` | `"Notifications ({hotkey})"` |

## ToastTitle

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ToastDescription

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ToastAction

### 属性

| 属性        | 类型                 | 默认值 |
| ----------- | -------------------- | ------- |
| `altText`\* | `string`             | -       |
| `as`        | `AsTag \| Component` | `"div"` |
| `asChild`   | `boolean`            | -       |

## ToastClose

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## ToastPortal

### 属性

| 属性         | 类型                    | 默认值 |
| ------------ | ----------------------- | ------- |
| `disabled`   | `boolean`               | -       |
| `forceMount` | `boolean`               | -       |
| `to`         | `string \| HTMLElement` | -       |
