# 进度

进度指示器

**部分：** `ProgressRoot`、`ProgressIndicator`

## ProgressRoot

### 属性

| 属性            | 类型                                       | 默认值                                       |
| --------------- | ------------------------------------------ | --------------------------------------------- |
| `as`            | `AsTag \| Component`                       | `"div"`                                       |
| `asChild`       | `boolean`                                  | -                                             |
| `getValueLabel` | `((value: number, max: number) => string)` | `${Math.round((value / max) * DEFAULT_MAX)}%` |
| `max`           | `number`                                   | `DEFAULT_MAX`                                 |
| `modelValue`    | `number \| null`                           | -                                             |

### 事件

| 事件               | 载荷             |
| ------------------- | ------------------- |
| `update:max`        | `[value: number]`   |
| `update:modelValue` | `[value: string[]]` |

### 插槽

| 插槽         | 类型                          |
| ------------ | ----------------------------- |
| `modelValue` | `number \| null \| undefined` |

## ProgressIndicator

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
