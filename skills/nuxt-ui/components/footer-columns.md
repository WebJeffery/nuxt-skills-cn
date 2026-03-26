# 页脚列

作为列的链接列表，用于在页脚中显示。

## 关键属性

- `columns`: 作为具有以下属性的对象数组：

- `label: string`{lang="ts-type"}
- `children?: FooterColumnLink[]`{lang="ts-type"}

每列包含定义链接的 `children` 对象数组。

## 用法

```vue
<UFooterColumns
  <!-- props here -->
/>
```
