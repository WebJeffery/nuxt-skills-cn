# 标签页

标签页内容面板

**部分：** `TabsRoot`、`TabsList`、`TabsTrigger`、`TabsContent`、`TabsIndicator`

## TabsRoot

### 属性

| 属性             | 类型                         | 默认值        |
| ---------------- | ---------------------------- | -------------- |
| `activationMode` | `"automatic" \| "manual"`    | `"automatic"`  |
| `as`             | `AsTag \| Component`         | `"div"`        |
| `asChild`        | `boolean`                    | -              |
| `defaultValue`   | `string \| number`           | -              |
| `dir`            | `"ltr" \| "rtl"`             | -              |
| `modelValue`     | `string \| number`           | -              |
| `orientation`    | `"vertical" \| "horizontal"` | `"horizontal"` |

### 事件

| 事件               | 载荷                     |
| ------------------- | --------------------------- |
| `update:modelValue` | `[payload: StringOrNumber]` |

### 插槽

| 插槽         | 类型               |
| ------------ | ------------------ |
| `modelValue` | `string \| number` |

## TabsList

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `loop`    | `boolean`            | `true`  |

## TabsTrigger

### 属性

| 属性       | 类型                 | 默认值    |
| ---------- | -------------------- | ---------- |
| `as`       | `AsTag \| Component` | `"button"` |
| `asChild`  | `boolean`            | -          |
| `disabled` | `boolean`            | `false`    |
| `value`\*  | `string \| number`   | -          |

## TabsContent

### 属性

| 属性         | 类型                 | 默认值 |
| ------------ | -------------------- | ------- |
| `as`         | `AsTag \| Component` | `"div"` |
| `asChild`    | `boolean`            | -       |
| `forceMount` | `boolean`            | -       |
| `value`\*    | `string \| number`   | -       |

## TabsIndicator

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
