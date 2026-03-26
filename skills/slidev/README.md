# Slidev Claude Code 技能

帮助 Claude Code 理解和使用 [Slidev](https://sli.dev) 演示文稿的代理技能。

## 安装

```bash
npx skills add slidevjs/slidev
```

这将把 Slidev 技能添加到您的 Claude Code 配置中。

## 包含内容

Slidev 技能为 Claude Code 提供了以下知识：

- **核心语法** - Markdown 语法、幻灯片分隔符、frontmatter
- **动画** - 点击动画、过渡效果、运动效果
- **代码功能** - 行高亮、Monaco 编辑器、代码组、magic-move
- **图表** - Mermaid、PlantUML、LaTeX 数学公式
- **布局** - 内置布局、插槽、全局图层
- **演讲者模式** - 录制、计时器、远程访问
- **导出** - PDF、PPTX、PNG、SPA 托管

## 使用方法

安装后，Claude Code 将在以下情况自动使用 Slidev 知识：

- 创建新演示文稿
- 添加包含代码示例的幻灯片
- 设置动画和过渡效果
- 配置主题和布局
- 导出演示文稿

### 示例提示

```
创建一个关于 TypeScript 泛型的 Slidev 演示文稿，包含代码示例
```

```
添加一个双列幻灯片，左侧是代码，右侧是解释
```

```
设置点击动画，逐个显示要点
```

```
配置演示文稿以导出 PDF 并包含演讲者备注
```

## 文档

- [Slidev 文档](https://sli.dev)
- [主题画廊](https://sli.dev/resources/theme-gallery)
- [展示案例](https://sli.dev/resources/showcases)

## 许可证

MIT
