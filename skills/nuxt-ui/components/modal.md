# 模态框

可用于显示消息或请求用户输入的对话框窗口。

> 基于 [Reka UI Modal](https://reka-ui.com/docs/components/dialog)

## 关键属性

- `title`: 设置模态框页眉的标题。
- `description`: 设置模态框页眉的描述。
- `close`: 自定义或隐藏模态框页眉中显示的关闭按钮（使用 `false` 值）。
- `transition`: 控制模态框是否动画。
- `overlay`: 控制模态框是否有遮罩。
- `modal`: 控制模态框是否阻止与外部内容的交互。
- `dismissible`: 控制模态框在单击外部或按 Escape 时是否可关闭。
- `scrollable`: 使模态框的内容在遮罩内可滚动。
- `fullscreen`: 使模态框全屏。

## 用法

```vue
<UModal
  <!-- props here -->
/>
```

## 插槽

- `#content`
- `#header`
- `#body`
- `#footer`
