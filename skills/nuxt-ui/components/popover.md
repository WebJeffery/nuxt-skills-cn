# 弹出框

围绕触发元素浮动的非模态对话框。

> 基于 [Reka UI Popover](https://reka-ui.com/docs/components/hover-card)

## 关键属性

- `mode`: 更改弹出框的模式。
- `content`: 控制弹出框内容的渲染方式，例如其 `align` 或 `side`。
- `arrow`: 在弹出框上显示箭头。
- `modal`: 控制弹出框是否阻止与外部内容的交互。
- `dismissible`: 控制弹出框在单击外部或按 Escape 时是否可关闭。

## 用法

```vue
<UPopover
  <!-- props here -->
/>
```

## 插槽

- `#content`
