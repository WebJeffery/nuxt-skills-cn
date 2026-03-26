# 切换组

具有组行为的多个切换

**部分：** `ToggleGroupRoot`、`ToggleGroupItem`

## ToggleGroupRoot

### 属性

| 属性           | 类型                         | 默认值 |
| -------------- | ---------------------------- | ------- |
| `as`           | `AsTag \| Component`         | `"div"` |
| `asChild`      | `boolean`                    | -       |
| `defaultValue` | `string \| string[]`         | -       |
| `dir`          | `"ltr" \| "rtl"`             | -       |
| `disabled`     | `boolean`                    | `false` |
| `loop`         | `boolean`                    | `true`  |
| `modelValue`   | `string \| string[]`         | -       |
| `orientation`  | `"vertical" \| "horizontal"` | -       |
| `rovingFocus`  | `boolean`                    | `true`  |
| `type`         | `"single" \| "multiple"`     | -       |

### 事件

| 事件               | 载荷                         |
| ------------------- | ------------------------------- |
| `update:modelValue` | `[payload: string \| string[]]` |

### 插槽

| 插槽         | 类型                              |
| ------------ | --------------------------------- |
| `modelValue` | `string \| string[] \| undefined` |

## ToggleGroupItem

### 属性

| 属性       | 类型                 | 默认值    |
| ---------- | -------------------- | ---------- |
| `as`       | `AsTag \| Component` | `"button"` |
| `asChild`  | `boolean`            | -          |
| `disabled` | `boolean`            | -          |
| `value`\*  | `string`             | -          |
