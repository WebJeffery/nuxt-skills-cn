---
name: block-frontmatter
description: 使用 YAML 代码块作为幻灯片 frontmatter 以支持语法高亮
---

# 块 Frontmatter

当您需要语法高亮和格式化程序支持时，使用 YAML 代码块作为幻灯片的 frontmatter。

## 用法

不要使用传统的 frontmatter `---`，而是在幻灯片开头使用 yaml 代码块：

````md
---
theme: default
---

# 幻灯片 1

---

```yaml
layout: quote
```

# 幻灯片 2

---

# 幻灯片 3
````

## 要点

- 仅适用于每张幻灯片的 frontmatter
- 不能用于 headmatter（演示文稿的第一个 frontmatter）
- 在编辑器中提供语法高亮
- 与 prettier-plugin-slidev 兼容
