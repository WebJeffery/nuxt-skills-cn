# 定价方案

可自定义的定价方案，用于在定价页面中显示。

## 关键属性

- `title`: 设置定价方案的标题。
- `description`: 设置定价方案的描述。
- `badge`: 在定价方案的标题旁边显示 [Badge](/docs/components/badge)。
- `price`: 设置定价方案的价格。
- `discount`: 设置折扣价格，将与原始价格一起显示（原始价格将显示删除线）。
- `features`: 作为字符串数组，在定价方案上显示功能列表：

## ::component-code

prettier: true
hide:

- class
  ignore:
- title
- description
- price
- features
  props:
  title: 'Solo'
  description: 'For bootstrappers and indie hackers.
- `button`: 使用 [Button](/docs/components/button) 组件的任何属性，在定价方案底部显示按钮。
- `variant`: 更改定价方案的变体。
- `orientation`: 更改定价方案的方向。
- `tagline`: 在价格上方显示标语文本。

## 用法

```vue
<UPricingPlan
  <!-- props here -->
/>
```
