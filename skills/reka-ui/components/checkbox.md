# 复选框

带有不确定状态的选择控制

**部分：** `CheckboxGroupRoot`、`CheckboxRoot`、`CheckboxIndicator`

## CheckboxRoot

### 属性

| 属性             | 类型                         | 默认值    |
| ---------------- | ---------------------------- | ---------- |
| `as`             | `AsTag \| Component`         | `"button"` |
| `asChild`        | `boolean`                    | -          |
| `checked`        | `boolean \| "indeterminate"` | -          |
| `defaultChecked` | `boolean`                    | -          |
| `disabled`       | `boolean`                    | -          |
| `id`             | `string`                     | -          |
| `name`           | `string`                     | -          |
| `required`       | `boolean`                    | -          |
| `value`          | `string`                     | `"on"`     |

### 事件

| 事件            | 载荷            |
| ---------------- | ------------------ |
| `update:checked` | `[value: boolean]` |

### 插槽

| 插槽      | 类型                               |
| --------- | ---------------------------------- |
| `checked` | `false \| true \| "indeterminate"` |

## CheckboxIndicator

### 属性

| 属性         | 类型                 | 默认值  |
| ------------ | -------------------- | -------- |
| `as`         | `AsTag \| Component` | `"span"` |
| `asChild`    | `boolean`            | -        |
| `forceMount` | `boolean`            | -        |
