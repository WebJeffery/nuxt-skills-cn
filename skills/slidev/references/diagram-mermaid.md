---
name: mermaid
description: 从文本描述创建图表
---

# Mermaid 图表

从文本描述创建图表。

## 基本用法

````md
```mermaid
sequenceDiagram
  Alice->John: 你好 John，你好吗？
  Note over Alice,John: 典型的交互
```
````

## 带选项

````md
```mermaid {theme: 'neutral', scale: 0.8}
graph TD
B[文本] --> C{决策}
C -->|选项1| D[结果 1]
C -->|选项2| E[结果 2]
```
````

## 图表类型

- `graph` / `flowchart` - 流程图
- `sequenceDiagram` - 序列图
- `classDiagram` - 类图
- `stateDiagram` - 状态图
- `erDiagram` - 实体关系图
- `gantt` - 甘特图
- `pie` - 饼图

## 资源

- Mermaid 文档：https://mermaid.js.org/
- 在线编辑器：https://mermaid.live/
