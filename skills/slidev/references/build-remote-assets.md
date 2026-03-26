---
name: remote-assets
description: 打包远程图像和资产以供离线使用
---

# 打包远程资产

远程图像在首次运行时自动缓存，以加快加载速度。

## 远程图像

```md
![远程图像](https://sli.dev/favicon.png)
```

由 vite-plugin-remote-assets 自动缓存。

## 本地图像

放置在 `public/` 文件夹中，并使用前导斜杠引用：

```md
![本地图像](/pic.png)
```

不要使用像 `./pic.png` 这样的相对路径。

## 自定义样式

转换为 img 标签以进行自定义大小/样式：

```html
<img src="/pic.png" class="m-40 h-40 rounded shadow" />
```
