---
name: slot-sugar
description: 多列布局中布局命名插槽的简写语法
---

# 布局的插槽简写

布局命名插槽的简写语法。

## 标准 Vue 插槽语法

```md
---
layout: two-cols
---

<template v-slot:default>

# 左侧

这显示在左侧

</template>
<template v-slot:right>

# 右侧

这显示在右侧

</template>
```

## 简写语法

```md
---
layout: two-cols
---

# 左侧

这显示在左侧

::right::

# 右侧

这显示在右侧
```

## 显式默认插槽

```md
---
layout: two-cols
---

::right::

# 右侧

这显示在右侧

::default::

# 左侧

这显示在左侧
```

## 带插槽的常用布局

- `two-cols`：`default`（左）和 `right`
- `two-cols-header`：`default`、`left`、`right`
- `image-left/right`：内容的 `default`
