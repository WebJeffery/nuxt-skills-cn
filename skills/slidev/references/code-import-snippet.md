---
name: import-snippet
description: 将代码从外部文件导入到幻灯片中，可选择区域
---

# 导入代码片段

将代码从外部文件导入到幻灯片中。

## 基本语法

```md
<<< @/snippets/snippet.js
```

`@` = 包根目录。建议：将片段放在 `@/snippets/` 中。

## 导入区域

使用 VS Code 区域语法：

```md
<<< @/snippets/snippet.js#region-name
```

## 指定语言

```md
<<< @/snippets/snippet.js ts
```

## 与功能结合使用

与行高亮、Monaco 编辑器结合使用：

```md
<<< @/snippets/snippet.js {2,3|5}{lines:true}
<<< @/snippets/snippet.js ts {monaco}{height:200px}
```

## 占位符

使用 `{*}` 作为行高亮占位符：

```md
<<< @/snippets/snippet.js {*}{lines:true}
```

## Monaco 写入

将编辑器链接到文件以进行实时编辑：

```md
<<< ./some-file.ts {monaco-write}
```
