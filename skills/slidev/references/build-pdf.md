---
name: pdf
description: 在 SPA 构建中包含可下载的 PDF
---

# 构建时生成 PDF

在构建的幻灯片旁边生成可下载的 PDF。

## 在 Headmatter 中启用

```md
---
download: true
---
```

这将生成 PDF 并向构建的幻灯片添加下载按钮。

## 自定义 PDF URL

跳过生成并使用现有的 PDF：

```md
---
download: 'https://example.com/my-talk.pdf'
---
```

## CLI 选项

```bash
slidev build --download
```

## 导出选项

通过以下方式配置 PDF 导出设置：
- CLI：`slidev build --download --with-clicks --timeout 60000`
- Headmatter：设置 `exportFilename`、`withClicks` 等。
