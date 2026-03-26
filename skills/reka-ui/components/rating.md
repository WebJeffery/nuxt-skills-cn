# 评分

星级评分输入（v2.8.0）

**部分：** `RatingRoot`、`RatingItem`、`RatingItemIndicator`

## RatingRoot

### 属性

| 属性           | 类型                         | 默认值        |
| -------------- | ---------------------------- | -------------- |
| `as`           | `AsTag \| Component`         | `"div"`        |
| `asChild`      | `boolean`                    | -              |
| `defaultValue` | `number`                     | -              |
| `modelValue`   | `number`                     | -              |
| `length`       | `number`                     | `5`            |
| `step`         | `1 \| 0.5 \| 0.25 \| 0.1`    | `1`            |
| `clearable`    | `boolean`                    | -              |
| `hoverable`    | `boolean`                    | -              |
| `disabled`     | `boolean`                    | `false`        |
| `orientation`  | `"vertical" \| "horizontal"` | `"horizontal"` |

### 事件

| 事件               | 载荷             |
| ------------------- | ------------------- |
| `update:modelValue` | `[payload: number]` |

### 插槽

| 插槽         | 类型                  |
| ------------ | --------------------- |
| `modelValue` | `number \| undefined` |
| `items`      | `number[]`            |

## RatingItem

### 属性

| 属性      | 类型                 | 默认值   |
| --------- | -------------------- | --------- |
| `as`      | `AsTag \| Component` | `"label"` |
| `asChild` | `boolean`            | -         |
| `item`    | `number`             | required  |

### 插槽

| 插槽    | 类型       |
| ------- | ---------- |
| `steps` | `number[]` |

## RatingItemIndicator

### 属性

| 属性      | 类型                 | 默认值  |
| --------- | -------------------- | -------- |
| `as`      | `AsTag \| Component` | -        |
| `asChild` | `boolean`            | -        |
| `step`    | `number`             | required |

### CSS 变量

| 变量                          | 描述            |
| --------------------------------- | ---------------------- |
| `--reka-rating-item-step-width`   | 基于步骤的宽度    |
| `--reka-rating-item-step-opacity` | 步骤的可见性     |
| `--reka-rating-item-step-z-index` | Z-index 堆叠顺序 |

### 数据属性

| 属性      | 值                   |
| -------------- | ----------------------- |
| `[data-state]` | `"active" \| undefined` |

## 示例

```vue
<script setup>
import { RatingRoot, RatingItem, RatingItemIndicator } from 'reka-ui'
const rating = ref(3)
</script>

<template>
  <RatingRoot v-model="rating" :length="5">
    <template #default="{ items }">
      <RatingItem v-for="item in items" :key="item" :item="item">
        <template #default="{ steps }">
          <RatingItemIndicator v-for="step in steps" :key="step" :step="step">
            ★
          </RatingItemIndicator>
        </template>
      </RatingItem>
    </template>
  </RatingRoot>
</template>
```
