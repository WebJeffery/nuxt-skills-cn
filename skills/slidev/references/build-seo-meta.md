---
name: seo-meta
description: 配置 SEO 和社交媒体元标签
---

# SEO 元标签

配置社交媒体和搜索引擎元标签。

## 配置

```yaml
---
seoMeta:
  ogTitle: Slidev 入门模板
  ogDescription: 面向开发者的演示文稿幻灯片
  ogImage: https://cover.sli.dev
  ogUrl: https://example.com
  twitterCard: summary_large_image
  twitterTitle: Slidev 入门模板
  twitterDescription: 面向开发者的演示文稿幻灯片
  twitterImage: https://cover.sli.dev
  twitterSite: username
  twitterUrl: https://example.com
---
```

## 可用选项

**Open Graph（Facebook、LinkedIn）：**
- `ogTitle` - 标题
- `ogDescription` - 描述
- `ogImage` - 预览图像 URL
- `ogUrl` - 规范 URL

**Twitter Card：**
- `twitterCard` - 卡片类型（summary、summary_large_image）
- `twitterTitle` - 标题
- `twitterDescription` - 描述
- `twitterImage` - 预览图像 URL
- `twitterSite` - Twitter 用户名

由 unhead 提供支持。
