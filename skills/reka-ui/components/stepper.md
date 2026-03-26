# 步骤器

多步骤进度指示器

**部分：** `StepperRoot`、`StepperItem`、`StepperTrigger`、`StepperTitle`、`StepperDescription`、`StepperIndicator`、`StepperSeparator`

## StepperRoot

### 属性

| 属性           | 类型                         | 默认值        |
| -------------- | ---------------------------- | -------------- |
| `as`           | `AsTag \| Component`         | `"div"`        |
| `asChild`      | `boolean`                    | -              |
| `defaultValue` | `number`                     | `1`            |
| `dir`          | `"ltr" \| "rtl"`             | -              |
| `linear`       | `boolean`                    | `true`         |
| `modelValue`   | `number`                     | -              |
| `orientation`  | `"vertical" \| "horizontal"` | `"horizontal"` |

### 事件

| 事件               | 载荷             |
| ------------------- | ------------------- |
| `update:modelValue` | `[payload: number]` |

### 插槽

| 插槽             | 类型                  |
| ---------------- | --------------------- |
| `modelValue`     | `number \| undefined` |
| `totalSteps`     | `number`              |
| `isNextDisabled` | `boolean`             |
| `isPrevDisabled` | `boolean`             |
| `isFirstStep`    | `boolean`             |
| `isLastStep`     | `boolean`             |
| `goToStep`       | ``                    |
| `nextStep`       | ``                    |
| `prevStep`       | ``                    |

## StepperItem

### 属性

| 属性        | 类型                 | 默认值 |
| ----------- | -------------------- | ------- |
| `as`        | `AsTag \| Component` | `"div"` |
| `asChild`   | `boolean`            | -       |
| `completed` | `boolean`            | `false` |
| `disabled`  | `boolean`            | `false` |
| `step`\*    | `number`             | -       |

### 插槽

| 插槽    | 类型                                    |
| ------- | --------------------------------------- |
| `state` | `"active" \| "completed" \| "inactive"` |

## StepperTrigger

### 属性

| 属性      | 类型                 | 默认值    |
| --------- | -------------------- | ---------- |
| `as`      | `AsTag \| Component` | `"button"` |
| `asChild` | `boolean`            | -          |

## StepperTitle

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"h4"`  |
| `asChild` | `boolean`            | -       |

## StepperDescription

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"p"`   |
| `asChild` | `boolean`            | -       |

## StepperIndicator

### 属性

| 属性      | 类型                 | 默认值 |
| --------- | -------------------- | ------- |
| `as`      | `AsTag \| Component` | `"div"` |
| `asChild` | `boolean`            | -       |

## StepperSeparator

### 属性

| 属性          | 类型                         | 默认值 |
| ------------- | ---------------------------- | ------- |
| `as`          | `AsTag \| Component`         | `"div"` |
| `asChild`     | `boolean`                    | -       |
| `decorative`  | `boolean`                    | -       |
| `orientation` | `"vertical" \| "horizontal"` | -       |
