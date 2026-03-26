# 工具栏

带有按钮/切换的工具栏

**部分：** `ToolbarRoot`、`ToolbarButton`、`ToolbarLink`、`ToolbarToggleGroup`、`ToolbarToggleItem`、`ToolbarSeparator`

## ToolbarRoot

### 属性

| 属性          | 类型                         | 默认值        |
| ------------- | ---------------------------- | -------------- |
| `as`          | `AsTag \| Component`         | `"div"`        |
| `asChild`     | `boolean`                    | -              |
| `dir`         | `"ltr" \| "rtl"`             | -              |
| `loop`        | `boolean`                    | -              |
| `orientation` | `"vertical" \| "horizontal"` | `"horizontal"` |

## ToolbarButton

### 属性

| 属性       | 类型                 | 默认值    |
| ---------- | -------------------- | ---------- |
| `as`       | `AsTag \| Component` | `"button"` |
| `asChild`  | `boolean`            | -          |
| `disabled` | `boolean`            | -          |

## ToolbarLink

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"a"`   |
| `asChild` | `boolean`            | -       |

## ToolbarToggleGroup

### 属性

| 属性           | 类型                         | 默认值 |
| -------------- | ---------------------------- | ------- |
| `as`           | `AsTag \| Component`         | `"div"` |
| `asChild`      | `boolean`                    | -       |
| `defaultValue` | `string \| string[]`         | -       |
| `dir`          | `"ltr" \| "rtl"`             | -       |
| `disabled`     | `boolean`                    | -       |
| `loop`         | `boolean`                    | -       |
| `modelValue`   | `string \| string[]`         | -       |
| `orientation`  | `"vertical" \| "horizontal"` | -       |
| `rovingFocus`  | `boolean`                    | -       |
| `type`         | `"single" \| "multiple"`     | -       |

### 事件

| 事件               | 载荷                         |
| ------------------- | ------------------------------- |
| `update:modelValue` | `[payload: string \| string[]]` |

## ToolbarToggleItem

### 属性

| 属性       | 类型                 | 默认值 |
| ---------- | -------------------- | ------- |
| `as`       | `AsTag \| Component` | `"div"` |
| `asChild`  | `boolean`            | -       |
| `disabled` | `boolean`            | -       |
| `value`\*  | `string`             | -       |

## ToolbarSeparator

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
