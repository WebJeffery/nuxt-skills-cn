---
name: importing-slides
description: 将演示文稿拆分为多个文件以实现可重用性
---

# 导入幻灯片

将演示文稿拆分为多个文件以实现可重用性。

## 基本导入

```md
# 标题

这是一个正常页面

---
src: ./pages/toc.md
---

<!-- 此处的内容被忽略 -->

---

# 第 4 页

另一个正常页面
```

## 导入特定幻灯片

使用哈希选择幻灯片：

```md
---
src: ./another-presentation.md#2,5-7
---
```

导入幻灯片 2、5、6 和 7。

## 重用幻灯片

多次导入同一文件：

```md
---
src: ./pages/toc.md
---

<!-- 稍后... -->

---
src: ./pages/toc.md
---
```

## Frontmatter 优先级

主条目 frontmatter 覆盖导入文件 frontmatter 中的重复键。
