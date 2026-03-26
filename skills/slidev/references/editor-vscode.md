---
name: vscode-extension
description: 在 VS Code 中可视化管理幻灯片
---

# VS Code 扩展

在 VS Code 中可视化管理幻灯片。

## 安装

从 VS Code Marketplace 安装：`antfu.slidev`

## 功能

- 在侧面板中预览幻灯片
- 幻灯片树视图
- 拖放以重新排序幻灯片
- 幻灯片块折叠
- 多项目支持
- 一键启动开发服务器

## 用法

1. 点击活动栏中的 `Slidev` 图标
2. 项目树显示工作区中的所有 Slidev 项目
3. 幻灯片树显示活动项目中的幻灯片
4. 预览面板显示实时预览

## 命令

在命令面板中输入 `Slidev` 以查看可用命令。

## 配置

将特定文件包含为 Slidev 条目：

```json
{
  "slidev.include": ["**/presentation.md"]
}
```

自定义开发命令：

```json
{
  "slidev.dev-command": "pnpm slidev ${args}"
}
```

## 占位符

- `${args}` - 所有 CLI 参数
- `${port}` - 端口号
