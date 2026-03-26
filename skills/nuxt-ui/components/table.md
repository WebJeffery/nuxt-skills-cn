# 表格

以行和列显示数据的响应式表格元素。

## 关键属性

- `data`: 作为对象数组，列将基于对象的键生成。
- `columns`: 作为 [ColumnDef](https://tanstack.
- `meta`: 作为对象 ([TableMeta](https://tanstack.
- `loading`: 显示加载状态，`loading-color` 属性更改其颜色，`loading-animation` 属性更改其动画。
- `sticky`: 使页眉或页脚粘性。
- `virtualize`: 为大型数据集启用虚拟化，作为布尔值或带有选项的对象，如 `{ estimateSize: 65, overscan: 12 }`。

## 用法

```vue
<UTable
  <!-- props here -->
/>
```
