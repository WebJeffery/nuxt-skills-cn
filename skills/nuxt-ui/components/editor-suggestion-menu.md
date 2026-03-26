# 编辑器建议菜单

命令菜单，在编辑器中输入 / 字符时显示格式和操作建议。

## 关键属性

- `items`: 作为具有以下属性的对象数组：

- [`kind?: "textAlign" | "heading" | "link" | "image" | "blockquote" | "bulletList" | "orderedList" | "codeBlock" | "horizontalRule" | "paragraph" | "clearFormatting" | "duplicate" | "delete" | "moveUp" | "moveDown" | "suggestion" | "mention" | "emoji"`{lang="ts-type"}](/docs/components/editor#handlers)
- `label?: string`{lang="ts-type"}
- `description?: string`{lang="ts-type"}
- `icon?: string`{lang="ts-type"}
- `type?: "label" | "separator"`{lang="ts-type"}
- `disabled?: boolean`{lang="ts-type"}

## ::component-example

elevated: true
collapse: true
name: 'editor-suggestion-menu-items-example'
class: 'p-8'

---

::

::note
您还可以将数组数组传递给 `items` 属性以创建分隔的项目组。

- `char`: 更改触发字符。
- `options`: 使用 [Floating UI 选项](https://floating-ui. 自定义定位行为。

## 用法

```vue
<UEditorSuggestionMenu
  <!-- props here -->
/>
```
