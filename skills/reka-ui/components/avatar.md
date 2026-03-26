# 头像

带有回退的用户图像

**部分：** `AvatarRoot`、`AvatarImage`、`AvatarFallback`

## AvatarRoot

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |

## AvatarImage

### 属性

| 属性             | 类型                                                   | 默认值 |
| ---------------- | ------------------------------------------------------ | ------- |
| `as`             | `AsTag \| Component`                                   | `"img"` |
| `asChild`        | `boolean`                                              | -       |
| `referrerPolicy` | `"" \| "no-referrer" \| "no-referrer-when-downgrad...` | -       |
| `src`\*          | `string`                                               | -       |

### 事件

| 事件                 | 载荷                       |
| --------------------- | ----------------------------- |
| `loadingStatusChange` | `[value: ImageLoadingStatus]` |

## AvatarFallback

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |
| `delayMs` | `number`             | `0`      |
