---
name: frontmatter
description: 单张幻灯片的配置选项
---

# 单张幻灯片的 Frontmatter

单张幻灯片的配置选项。

## 布局

```yaml
---
layout: center
---
```

可用布局：`default`、`cover`、`center`、`two-cols`、`two-cols-header`、`image`、`image-left`、`image-right`、`iframe`、`iframe-left`、`iframe-right`、`quote`、`section`、`statement`、`fact`、`full`、`intro`、`end`、`none`

## 背景

```yaml
---
background: /image.jpg
backgroundSize: cover
class: text-white
---
```

## 点击次数

```yaml
---
clicks: 5                   # 此幻灯片的总点击次数
clicksStart: 0              # 起始点击次数
---
```

## 过渡效果

```yaml
---
transition: fade            # 幻灯片过渡效果
---
```

或前进/后退使用不同的过渡效果：

```yaml
---
transition: slide-left | slide-right
---
```

## 缩放

```yaml
---
zoom: 0.8                   # 缩放内容（0.8 = 80%）
---
```

## 隐藏幻灯片

```yaml
---
disabled: true              # 隐藏此幻灯片
# 或
hide: true
---
```

## 目录

```yaml
---
hideInToc: true             # 从目录组件中隐藏
level: 2                    # 覆盖标题级别
title: 自定义标题         # 覆盖幻灯片标题
---
```

## 导入外部文件

```yaml
---
src: ./slides/intro.md      # 导入 markdown 文件
---
```

导入特定幻灯片：

```yaml
---
src: ./other.md#2,5-7       # 导入幻灯片 2、5、6、7
---
```

## 路由别名

```yaml
---
routeAlias: intro           # URL 使用 /intro 而不是 /1
---
```

## 预加载

```yaml
---
preload: false              # 进入前不挂载
---
```

## 可拖动位置

```yaml
---
dragPos:
  logo: 100,50,200,100,0    # 左、上、宽、高、旋转
  arrow: 300,200,50,50,45
---
```

## 图片布局

```yaml
---
layout: image-left
image: /photo.jpg
backgroundSize: contain
class: my-custom-class
---
```

## Iframe 布局

```yaml
---
layout: iframe
url: https://example.com
---
```

## 两列

```yaml
---
layout: two-cols
---

# 左侧

内容

::right::

# 右侧

内容
```

## 带标题的两列

```yaml
---
layout: two-cols-header
---

# 标题

::left::

左侧内容

::right::

右侧内容
```

## 完整示例

```yaml
---
layout: center
background: /bg.jpg
class: text-white text-center
transition: fade
clicks: 3
zoom: 0.9
hideInToc: false
---

# 幻灯片内容
```
