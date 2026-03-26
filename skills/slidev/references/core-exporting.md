---
name: exporting
description: 导出幻灯片为 PDF、PPTX、PNG 或 Markdown
---

# 导出幻灯片

导出演示文稿为 PDF、PPTX、PNG 或 Markdown。

## 浏览器导出器

访问 `http://localhost:3030/export`：
- 选择格式和选项
- 预览和下载

## CLI 导出

需要 playwright：
```bash
pnpm add -D playwright-chromium
```

### PDF 导出

```bash
slidev export
slidev export --output my-slides.pdf
```

### PowerPoint 导出

```bash
slidev export --format pptx
```

### PNG 导出

```bash
slidev export --format png
slidev export --format png --range 1-5
```

### Markdown 导出

```bash
slidev export --format md
```

## 导出选项

### 包含点击步骤

将每次点击导出为单独页面：
```bash
slidev export --with-clicks
```

### 暗色模式

```bash
slidev export --dark
```

### 幻灯片范围

```bash
slidev export --range 1,4-7,10
```

### 目录

带可点击大纲的 PDF：
```bash
slidev export --with-toc
```

### 超时

针对渲染缓慢的幻灯片：
```bash
slidev export --timeout 60000
```

### 等待

捕获前等待：
```bash
slidev export --wait 2000
```

### 等待条件

等待条件：
```bash
slidev export --wait-until networkidle   # 默认
slidev export --wait-until domcontentloaded
slidev export --wait-until load
slidev export --wait-until none
```

### 透明背景

```bash
slidev export --omit-background
```

### 自定义浏览器

```bash
slidev export --executable-path /path/to/chrome
```

## Headmatter 选项

```yaml
---
exportFilename: my-presentation
download: true              # 在构建中添加下载按钮
export:
  format: pdf
  timeout: 30000
  withClicks: false
---
```

## 故障排除

### 内容缺失

增加等待时间：
```bash
slidev export --wait 3000 --timeout 60000
```

### 全局图层状态错误

使用 `--per-slide` 或使用 `slide-top.vue` 代替 `global-top.vue`。

### 表情符号显示异常

使用系统字体或在服务器上安装表情符号字体。

### CI/CD 导出

安装 playwright 浏览器：
```bash
npx playwright install chromium
```
