# 滚动区域

自定义滚动条容器

**部分：** `ScrollAreaRoot`、`ScrollAreaViewport`、`ScrollAreaScrollbar`、`ScrollAreaThumb`、`ScrollAreaCorner`

## ScrollAreaRoot

### 属性

| 属性              | 类型                                        | 默认值   |
| ----------------- | ------------------------------------------- | --------- |
| `as`              | `AsTag \| Component`                        | `"div"`   |
| `asChild`         | `boolean`                                   | -         |
| `dir`             | `"ltr" \| "rtl"`                            | -         |
| `scrollHideDelay` | `number`                                    | `600`     |
| `type`            | `"always" \| "scroll" \| "hover" \| "auto"` | `"hover"` |

## ScrollAreaViewport

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
| `nonce`   | `string`             | -       |

## ScrollAreaScrollbar

### 属性

| 属性          | 类型                         | 默认值      |
| ------------- | ---------------------------- | ------------ |
| `as`          | `AsTag \| Component`         | `"div"`      |
| `asChild`     | `boolean`                    | -            |
| `forceMount`  | `boolean`                    | -            |
| `orientation` | `"vertical" \| "horizontal"` | `"vertical"` |

## ScrollAreaThumb

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## ScrollAreaCorner

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |
