# 可折叠

单个可折叠面板

**部分：** `CollapsibleRoot`、`CollapsibleTrigger`、`CollapsibleContent`

## CollapsibleRoot

### 属性

| 属性          | 类型                 | 默认值 |
| ------------- | -------------------- | ------- |
| `as`          | `AsTag \| Component` | `"div"` |
| `asChild`     | `boolean`            | -       |
| `defaultOpen` | `boolean`            | `false` |
| `disabled`    | `boolean`            | -       |
| `open`        | `boolean`            | -       |

### 事件

| 事件         | 载荷            |
| ------------- | ------------------ |
| `update:open` | `[value: boolean]` |

### 插槽

| 插槽   | 类型      |
| ------ | --------- |
| `open` | `boolean` |

## CollapsibleTrigger

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## CollapsibleContent

### 属性

| 属性         | 类型                 | 默认值 |
| ------------ | -------------------- | ------- |
| `as`         | `AsTag \| Component` | `"div"` |
| `asChild`    | `boolean`            | -       |
| `forceMount` | `boolean`            | -       |
