# 命令面板

由 Fuse.js 提供支持的全文本搜索命令面板，用于高效的模糊匹配。

> 基于 [Reka UI CommandPalette](https://reka-ui.com/docs/components/listbox)

## 关键属性

- `groups`: 作为具有以下属性的对象数组：

- `id: string`{lang="ts-type"}
- `label?: string`{lang="ts-type"}
- `slot?: string`{lang="ts-type"}
- `items?: CommandPaletteItem[]`{lang="ts-type"}
- [`ignoreFilter?: boolean`{lang="ts-type"}](#with-ignore-filter)
- [`postFilter?: (searchTerm: string, items: T[]) => T[]`{lang="ts-type"}](#with-post-filtered-items)
- `highlightedIcon?: string`{lang="ts-type"}

::caution
您必须为每个组提供 `id`，否则组将被忽略。

- `multiple`: 允许多选。
- `placeholder`: 更改占位符文本。
- `icon`: 自定义输入 [Icon](/docs/components/icon)。
- `loading`: 在命令面板上显示加载图标。
- `close`: 显示一个 [Button](/docs/components/button) 来关闭命令面板。
- `back`: 自定义或隐藏导航到子菜单时显示的后退按钮（使用 `false` 值）。
- `disabled`: 禁用命令面板。
- `virtualize`: 为大型列表启用虚拟化，作为布尔值或带有选项的对象，如 `{ estimateSize: 32, overscan: 12 }`。
- `slot`:

## 用法

```vue
<UCommandPalette
  <!-- props here -->
/>
```

## 插槽

- `#footer`
- `#item`
