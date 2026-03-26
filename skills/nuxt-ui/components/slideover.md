# 侧边滑窗

从屏幕任意侧滑入的对话框。

> 基于 [Reka UI Slideover](https://reka-ui.com/docs/components/dialog)

## 关键属性

- `title`: 设置侧边滑窗页眉的标题。
- `description`: 设置侧边滑窗页眉的描述。
- `close`: 自定义或隐藏侧边滑窗页眉中显示的关闭按钮（使用 `false` 值）。
- `side`: 设置侧边滑窗从屏幕的哪一侧滑入。
- `transition`: 控制侧边滑窗是否动画。
- `overlay`: 控制侧边滑窗是否有遮罩。
- `modal`: 控制侧边滑窗是否阻止与外部内容的交互。
- `dismissible`: 控制侧边滑窗在单击外部或按 Escape 时是否可关闭。

## 用法

```vue
<USlideover
  <!-- props here -->
/>
```

## 插槽

- `#content`
- `#header`
- `#body`
- `#footer`
