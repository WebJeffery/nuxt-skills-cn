---
name: frontmatter-merging
description: 导入具有冲突 frontmatter 的幻灯片时的优先级规则
---

# Frontmatter 合并

导入幻灯片时，主条目的 frontmatter 优先。

## 示例

主文件（`slides.md`）：
```md
---
src: ./cover.md
background: https://sli.dev/bar.png
class: text-center
---
```

导入文件（`cover.md`）：
```md
---
layout: cover
background: https://sli.dev/foo.png
---

# 封面

封面页
```

## 结果

```md
---
layout: cover
background: https://sli.dev/bar.png  # 主条目获胜
class: text-center
---

# 封面

封面页
```

## 优先级规则

对于重复键，主条目 > 导入文件。
