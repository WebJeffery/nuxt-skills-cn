---
name: og-image
description: 为社交分享配置 Open Graph 预览图像
---

# Open Graph 图像

设置社交媒体共享的预览图像。

## 自定义 URL

```md
---
seoMeta:
  ogImage: https://url.to.your.image.png
---
```

## 本地图像

将 `./og-image.png` 放置在项目根目录中 - Slidev 会自动使用它。

## 自动生成

从第一张幻灯片生成：

```md
---
seoMeta:
  ogImage: auto
---
```

使用 Playwright 捕获第一张幻灯片。需要安装 playwright。

生成的图像保存为 `./og-image.png` - 可以提交到仓库中。
