# 编辑器表情符号菜单

表情符号选择器菜单，在编辑器中输入 : 字符时显示表情符号建议。

## 关键属性

- `items`: 作为具有以下属性的对象数组：

- `name: string`{lang="ts-type"}
- `emoji: string`{lang="ts-type"}
- `shortcodes?: string[]`{lang="ts-type"}
- `tags?: string[]`{lang="ts-type"}
- `group?: string`{lang="ts-type"}
- `fallbackImage?: string`{lang="ts-type"}

## ::component-example

elevated: true
collapse: true
name: 'editor-emoji-menu-items-example'
class: 'p-8'

---

::

::note
您还可以将数组数组传递给 `items` 属性以创建分隔的项目组。

- `char`: 更改触发字符。
- `options`: 使用 [Floating UI 选项](https://floating-ui. 自定义定位行为。

## 用法

```vue
<UEditorEmojiMenu
  <!-- props here -->
/>
```
