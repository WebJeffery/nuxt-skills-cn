---
name: prettier-plugin
description: 正确格式化 Slidev markdown 文件
---

# Prettier 插件

正确格式化 Slidev markdown 文件。

## 安装

```bash
pnpm i -D prettier prettier-plugin-slidev
```

## 配置

创建/修改 `.prettierrc`：

```json
{
  "overrides": [
    {
      "files": ["slides.md", "pages/*.md"],
      "options": {
        "parser": "slidev",
        "plugins": ["prettier-plugin-slidev"]
      }
    }
  ]
}
```

## 为什么需要

Slidev 的语法（frontmatter、代码块）可能与默认的 Markdown 格式化冲突。此插件理解 Slidev 特定的语法。

## 注意

必须通过 `overrides` 指定文件，因为 Slidev 和常规 Markdown 共享 `.md` 扩展名。
