# 抽屉

平滑滑入和滑出屏幕的抽屉。

## 关键属性

- `title`: 设置抽屉页眉的标题。
- `description`: 设置抽屉页眉的描述。
- `direction`: 控制抽屉的方向。
- `inset`: 从边缘插入抽屉。
- `handle`: 控制抽屉是否有手柄。
- `overlay`: 控制抽屉是否有遮罩。
- `modal`: 控制抽屉是否阻止与外部内容的交互。
- `dismissible`: 控制抽屉在单击外部或按 Escape 时是否可关闭。

## 用法

```vue
<UDrawer
  <!-- props here -->
/>
```

## 插槽

- `#content`
- `#header`
- `#body`
- `#footer`
