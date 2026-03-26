# 开关

在两种状态之间切换

**部分：** `SwitchRoot`、`SwitchThumb`

## SwitchRoot

### 属性

| 属性             | 类型                 | 默认值    |
| ---------------- | -------------------- | ---------- |
| `as`             | `AsTag \| Component` | `"button"` |
| `asChild`        | `boolean`            | -          |
| `checked`        | `boolean`            | -          |
| `defaultChecked` | `boolean`            | -          |
| `disabled`       | `boolean`            | -          |
| `id`             | `string`             | -          |
| `name`           | `string`             | -          |
| `required`       | `boolean`            | -          |
| `value`          | `string`             | `"on"`     |

### 事件

| 事件            | 载荷              |
| ---------------- | -------------------- |
| `update:checked` | `[payload: boolean]` |

### 插槽

| 插槽      | 类型      |
| --------- | --------- |
| `checked` | `boolean` |

## SwitchThumb

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | `"span"` |
| `asChild` | `boolean`            | -        |
