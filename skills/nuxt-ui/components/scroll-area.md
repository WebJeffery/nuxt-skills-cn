# 滚动区域

创建可滚动容器，支持大型列表的可选虚拟化。

> 基于 [Reka UI ScrollArea](https://reka-ui.com/docs/components/scroll-area)

## 关键属性

- `orientation`: 控制滚动方向（`'vertical'` 或 `'horizontal'`）。
- `items`: 在可滚动区域内渲染的数据数组。
- `virtualize`: 为大型数据集启用性能优化（仅渲染可见项目）。支持 `estimateSize`、`lanes`、`gap`。
- `as`: 指定底层 HTML 元素或组件（默认为 `'div'`）。
- `ui`: 用于样式化根、视口和项目的自定义对象。

## 用法

```vue
<UScrollArea
  <!-- props here -->
/>
```
