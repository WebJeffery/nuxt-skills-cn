# 手风琴

可折叠的内容部分

**部分：** `AccordionRoot`、`AccordionItem`、`AccordionHeader`、`AccordionTrigger`、`AccordionContent`

## AccordionRoot

### 属性

| 属性           | 类型                         | 默认值      |
| -------------- | ---------------------------- | ------------ |
| `as`           | `AsTag \| Component`         | `"div"`      |
| `asChild`      | `boolean`                    | -            |
| `collapsible`  | `boolean`                    | `false`      |
| `defaultValue` | `string \| string[]`         | -            |
| `dir`          | `"ltr" \| "rtl"`             | -            |
| `disabled`     | `boolean`                    | `false`      |
| `modelValue`   | `string \| string[]`         | -            |
| `orientation`  | `"vertical" \| "horizontal"` | `"vertical"` |
| `type`         | `"single" \| "multiple"`     | -            |

### 事件

| 事件               | 载荷                       |
| ------------------- | ----------------------------- |
| `update:modelValue` | `[value: string \| string[]]` |

### 插槽

| 插槽         | 类型                              |
| ------------ | --------------------------------- |
| `modelValue` | `string \| string[] \| undefined` |

## AccordionItem

### 属性

| 属性       | 类型                 | 默认值 |
| ---------- | -------------------- | ------- |
| `as`       | `AsTag \| Component` | `"div"` |
| `asChild`  | `boolean`            | -       |
| `disabled` | `boolean`            | -       |
| `value`\*  | `string`             | -       |

### 插槽

| 插槽   | 类型      |
| ------ | --------- |
| `open` | `boolean` |

## AccordionHeader

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"h3"`  |
| `asChild` | `boolean`            | -       |

## AccordionTrigger

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## AccordionContent

### 属性

| 属性         | 类型                 | 默认值 |
| ------------ | -------------------- | ------- |
| `as`         | `AsTag \| Component` | `"div"` |
| `asChild`    | `boolean`            | -       |
| `forceMount` | `boolean`            | -       |
