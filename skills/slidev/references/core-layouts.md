---
name: layouts
description: 可用的幻灯片布局
---

# 内置布局

可用的幻灯片布局。

## 基本布局

### default

标准幻灯片布局。
```yaml
---
layout: default
---
```

### center

内容水平和垂直居中。
```yaml
---
layout: center
---
```

### cover

标题/封面幻灯片，内容居中。
```yaml
---
layout: cover
---
```

### end

结束幻灯片。
```yaml
---
layout: end
---
```

### full

全屏内容，无内边距。
```yaml
---
layout: full
---
```

### none

无布局样式。
```yaml
---
layout: none
---
```

## 文本布局

### intro

介绍幻灯片。
```yaml
---
layout: intro
---
```

### quote

大型引用展示。
```yaml
---
layout: quote
---
```

### section

章节分隔符。
```yaml
---
layout: section
---
```

### statement

陈述/声明展示。
```yaml
---
layout: statement
---
```

### fact

事实/数据展示。
```yaml
---
layout: fact
---
```

## 多列布局

### two-cols

两列并排：
```md
---
layout: two-cols
---

# 左列

左列内容

::right::

# 右列

右列内容
```

### two-cols-header

标题下方两列：
```md
---
layout: two-cols-header
---

# 标题

::left::

左列内容

::right::

右列内容
```

## 图片布局

### image

全屏图片：
```yaml
---
layout: image
image: /photo.jpg
backgroundSize: cover
---
```

### image-left

左侧图片，右侧内容：
```yaml
---
layout: image-left
image: /photo.jpg
class: my-class
---

# 右侧内容
```

### image-right

右侧图片，左侧内容：
```yaml
---
layout: image-right
image: /photo.jpg
---

# 左侧内容
```

属性：`image`、`class`、`backgroundSize`

## Iframe 布局

### iframe

全屏 iframe：
```yaml
---
layout: iframe
url: https://example.com
---
```

### iframe-left

左侧 iframe，右侧内容：
```yaml
---
layout: iframe-left
url: https://example.com
---

# 内容
```

### iframe-right

右侧 iframe，左侧内容：
```yaml
---
layout: iframe-right
url: https://example.com
---

# 内容
```

## 布局加载顺序

1. Slidev 默认布局
2. 主题布局
3. 插件布局
4. 自定义布局（`./layouts/`）

后加载的源会覆盖先加载的。

## 自定义布局

创建 `layouts/my-layout.vue`：

```vue
<template>
  <div class="slidev-layout my-layout">
    <slot />
  </div>
</template>

<style scoped>
.my-layout {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
```

带命名插槽：

```vue
<template>
  <div class="slidev-layout two-areas">
    <div class="top">
      <slot name="top" />
    </div>
    <div class="bottom">
      <slot />
    </div>
  </div>
</template>
```

用法：
```md
---
layout: two-areas
---

::top::

顶部内容

::default::

底部内容
```
