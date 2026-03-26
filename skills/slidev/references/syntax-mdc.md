---
name: comark
description: Comark 语法支持
---

# Comark 语法

带有组件和样式语法的增强 Markdown。

## 启用

```md
---
comark: true
---
```

## 内联样式

```md
这是一段 [红色文本]{style="color:red"}
```

## 内联组件

```md
:inline-component{prop="value"}
```

## 图像属性

```md
![](/image.png){width=500px lazy}
```

## 块组件

```md
::block-component{prop="value"}
**默认**插槽内容
::
```

## 使用场景

- 添加内联样式而无需 HTML
- 内联使用 Vue 组件
- 向图像添加属性
- 创建复杂的组件布局

基于 Comark 语法。
