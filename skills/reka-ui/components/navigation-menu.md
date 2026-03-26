# 导航菜单

网站导航菜单

**部分：** `NavigationMenuRoot`、`NavigationMenuList`、`NavigationMenuItem`、`NavigationMenuTrigger`、`NavigationMenuContent`、`NavigationMenuLink`、`NavigationMenuIndicator`、`NavigationMenuViewport`、`NavigationMenuSub`

## NavigationMenuRoot

### 属性

| 属性                  | 类型                         | 默认值        |
| --------------------- | ---------------------------- | -------------- |
| `as`                  | `AsTag \| Component`         | `"nav"`        |
| `asChild`             | `boolean`                    | -              |
| `defaultValue`        | `string`                     | -              |
| `delayDuration`       | `number`                     | `200`          |
| `dir`                 | `"ltr" \| "rtl"`             | -              |
| `disableClickTrigger` | `boolean`                    | `false`        |
| `disableHoverTrigger` | `boolean`                    | `false`        |
| `modelValue`          | `string`                     | -              |
| `orientation`         | `"vertical" \| "horizontal"` | `"horizontal"` |
| `skipDelayDuration`   | `number`                     | `300`          |

### 事件

| 事件               | 载荷           |
| ------------------- | ----------------- |
| `update:modelValue` | `[value: string]` |

### 插槽

| 插槽         | 类型     |
| ------------ | -------- |
| `modelValue` | `string` |

## NavigationMenuList

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"ul"`  |
| `asChild` | `boolean`            | -       |

## NavigationMenuItem

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"li"`  |
| `asChild` | `boolean`            | -       |
| `value`   | `string`             | -       |

## NavigationMenuTrigger

### 属性

| 属性       | 类型                 | 默认值    |
| ---------- | -------------------- | ---------- |
| `as`       | `AsTag \| Component` | `"button"` |
| `asChild`  | `boolean`            | -          |
| `disabled` | `boolean`            | -          |

## NavigationMenuContent

### 属性

| 属性                          | 类型                 | 默认值 |
| ----------------------------- | -------------------- | ------- |
| `as`                          | `AsTag \| Component` | `"div"` |
| `asChild`                     | `boolean`            | -       |
| `disableOutsidePointerEvents` | `boolean`            | -       |
| `forceMount`                  | `boolean`            | -       |

### 事件

| 事件                | 载荷                                               |
| -------------------- | ----------------------------------------------------- |
| `escapeKeyDown`      | `[event: KeyboardEvent]`                              |
| `focusOutside`       | `[event: FocusOutsideEvent]`                          |
| `interactOutside`    | `[event: PointerDownOutsideEvent \| FocusOutsideE...` |
| `pointerDownOutside` | `[event: PointerDownOutsideEvent]`                    |

## NavigationMenuLink

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `active`  | `boolean`            | -       |
| `as`      | `AsTag \| Component` | `"a"`   |
| `asChild` | `boolean`            | -       |

### 事件

| 事件    | 载荷                                             |
| -------- | --------------------------------------------------- |
| `select` | `[payload: CustomEvent<{ originalEvent: Event; }>]` |

## NavigationMenuIndicator

### 属性

| 属性         | 类型                 | 默认值 |
| ------------ | -------------------- | ------- |
| `as`         | `AsTag \| Component` | `"div"` |
| `asChild`    | `boolean`            | -       |
| `forceMount` | `boolean`            | -       |

## NavigationMenuViewport

### 属性

| 属性         | 类型                 | 默认值 |
| ------------ | -------------------- | ------- |
| `as`         | `AsTag \| Component` | `"div"` |
| `asChild`    | `boolean`            | -       |
| `forceMount` | `boolean`            | -       |

## NavigationMenuSub

### 属性

| 属性           | 类型                         | 默认值        |
| -------------- | ---------------------------- | -------------- |
| `as`           | `AsTag \| Component`         | `"div"`        |
| `asChild`      | `boolean`                    | -              |
| `defaultValue` | `string`                     | -              |
| `modelValue`   | `string`                     | -              |
| `orientation`  | `"vertical" \| "horizontal"` | `"horizontal"` |

### 事件

| 事件               | 载荷           |
| ------------------- | ----------------- |
| `update:modelValue` | `[value: string]` |

### 插槽

| 插槽         | 类型     |
| ------------ | -------- |
| `modelValue` | `string` |
