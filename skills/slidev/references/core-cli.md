---
name: cli
description: Slidev 命令行界面参考
---

# CLI 命令

Slidev 命令行界面参考。

## 开发服务器

```bash
slidev [entry]
slidev slides.md
```

选项：
| 选项 | 默认值 | 描述 |
|--------|---------|-------------|
| `--port` | 3030 | 服务器端口 |
| `--open` | false | 打开浏览器 |
| `--remote [password]` | - | 启用远程访问 |
| `--bind` | 0.0.0.0 | 绑定地址 |
| `--base` | / | 基础 URL 路径 |
| `--log` | warn | 日志级别 |
| `--force` | false | 强制优化器重新打包 |
| `--theme` | - | 覆盖主题 |

示例：
```bash
slidev --port 8080 --open
slidev --remote mypassword
slidev --base /talks/my-talk/
```

## 构建

```bash
slidev build [entry]
```

选项：
| 选项 | 默认值 | 描述 |
|--------|---------|-------------|
| `--out` | dist | 输出目录 |
| `--base` | / | 部署的基础 URL |
| `--download` | false | 包含 PDF 下载 |
| `--theme` | - | 覆盖主题 |
| `--without-notes` | false | 排除演讲者备注 |

示例：
```bash
slidev build --base /my-repo/
slidev build --download --out public
slidev build slides1.md slides2.md  # 多个构建
```

## 导出

```bash
slidev export [entry]
```

选项：
| 选项 | 默认值 | 描述 |
|--------|---------|-------------|
| `--output` | - | 输出文件名 |
| `--format` | pdf | pdf / png / pptx / md |
| `--timeout` | 30000 | 每张幻灯片超时（毫秒） |
| `--range` | - | 幻灯片范围（例如 1,4-7） |
| `--dark` | false | 导出暗色模式 |
| `--with-clicks` | false | 包含点击步骤 |
| `--with-toc` | false | PDF 目录 |
| `--wait` | 0 | 导出前等待毫秒 |
| `--wait-until` | networkidle | 等待条件 |
| `--omit-background` | false | 透明背景 |
| `--executable-path` | - | 浏览器路径 |

示例：
```bash
slidev export
slidev export --format pptx
slidev export --format png --range 1-5
slidev export --with-clicks --dark
slidev export --timeout 60000 --wait 2000
```

## 格式化

```bash
slidev format [entry]
```

格式化幻灯片 markdown 文件。

## 主题弹出

```bash
slidev theme eject [entry]
```

选项：
| 选项 | 默认值 | 描述 |
|--------|---------|-------------|
| `--dir` | theme | 输出目录 |
| `--theme` | - | 要弹出的主题 |

将主题提取到本地目录以进行自定义。

## npm 脚本用法

在 package.json 中：
```json
{
  "scripts": {
    "dev": "slidev",
    "build": "slidev build",
    "export": "slidev export"
  }
}
```

带参数（注意 `--`）：
```bash
npm run dev -- --port 8080 --open
npm run export -- --format pptx
```

## 布尔选项

```bash
slidev --open           # 等同于 --open true
slidev --no-open        # 等同于 --open false
```

## 全局安装 CLI

```bash
npm i -g @slidev/cli
```
