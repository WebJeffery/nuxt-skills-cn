---
name: monaco-write
description: 编辑代码并将更改保存回文件
---

# 可写 Monaco 编辑器

编辑代码并将更改保存回文件。

## 用法

```md
<<< ./some-file.ts {monaco-write}
```

## 行为

- 将 Monaco 编辑器链接到文件系统上的实际文件
- 更改直接保存到文件
- 适用于实时编码演示

## 警告

使用前备份文件 - 更改会直接保存。
