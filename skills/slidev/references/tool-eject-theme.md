---
name: eject-theme
description: 将主题提取到本地文件系统以进行自定义
---

# 弹出主题

将安装的主题提取到本地文件系统以进行自定义。

## 命令

```bash
slidev theme eject
```

## 结果

- 主题文件复制到 `./theme/`
- Frontmatter 更新为 `theme: ./theme`

## 使用场景

- 完全控制主题
- 基于现有主题创建新主题
- 在不修改 node_modules 的情况下进行自定义

如果创建衍生主题，请致谢原始主题和作者。
