# CLI 命令

Nuxt 通过 `nuxi`（或 `npx nuxt`）提供 CLI 命令，用于开发、构建和项目管理。

## 项目初始化

### 创建新项目

```bash
# 交互式项目创建
npx nuxi@latest init my-app

# 使用特定包管理器
npx nuxi@latest init my-app --packageManager pnpm

# 使用模块
npx nuxi@latest init my-app --modules "@nuxt/ui,@nuxt/image"

# 从模板创建
npx nuxi@latest init my-app --template v3

# 跳过模块选择提示
npx nuxi@latest init my-app --no-modules
```

**选项：**
| 选项 | 描述 |
|--------|-------------|
| `-t, --template` | 模板名称 |
| `--packageManager` | npm、pnpm、yarn 或 bun |
| `-M, --modules` | 要安装的模块（逗号分隔） |
| `--gitInit` | 初始化 git 仓库 |
| `--no-install` | 跳过安装依赖 |

## 开发

### 启动开发服务器

```bash
# 启动开发服务器（默认：http://localhost:3000）
npx nuxt dev

# 自定义端口
npx nuxt dev --port 4000

# 在浏览器中打开
npx nuxt dev --open

# 监听所有接口（用于移动端测试）
npx nuxt dev --host 0.0.0.0

# 启用 HTTPS
npx nuxt dev --https

# 重启时清空控制台
npx nuxt dev --clear

# 创建公共隧道
npx nuxt dev --tunnel
```

**选项：**
| 选项 | 描述 |
|--------|-------------|
| `-p, --port` | 监听端口 |
| `-h, --host` | 监听主机 |
| `-o, --open` | 在浏览器中打开 |
| `--https` | 启用 HTTPS |
| `--tunnel` | 创建公共隧道（通过 untun） |
| `--qr` | 显示移动端二维码 |
| `--clear` | 重启时清空控制台 |

**环境变量：**
- `NUXT_PORT` 或 `PORT` - 默认端口
- `NUXT_HOST` 或 `HOST` - 默认主机

## 构建

### 生产环境构建

```bash
# 构建生产环境
npx nuxt build

# 预渲染构建
npx nuxt build --prerender

# 使用特定预设构建
npx nuxt build --preset node-server
npx nuxt build --preset cloudflare-pages
npx nuxt build --preset vercel

# 使用环境构建
npx nuxt build --envName staging
```

**选项：**
| 选项 | 描述 |
|--------|-------------|
| `--preset` | 构建预设（node-server、cloudflare-pages、vercel） |
| `--prerender` | 启用预渲染 |
| `--dotenv` | 环境配置文件 |
| `--logLevel` | 日志级别 |

## 生成静态站点

```bash
# 生成静态站点
npx nuxt generate

# 生成后预览
npx nuxt preview
```

## 分析和检查

```bash
# 分析包大小
npx nuxt analyze

# 类型检查
npx nuxi typecheck

# ESLint 检查
npx nuxi lint

# 检查项目状态
npx nuxi prepare
```

## 开发工具

```bash
# 添加模块
npx nuxi module add <module-name>

# 模块信息
npx nuxi module info

# 清理缓存
npx nuxt clean
```

## 开发工作流最佳实践

### 启动开发服务器

```bash
# 推荐的开发服务器启动方式
nuxt dev
```

**最佳实践：**
- 使用 `--clear` 避免控制台输出混乱
- 使用 `--qr` 和 `--host` 进行移动端测试
- 在 CI/CD 环境中使用环境变量配置端口

### 开发环境配置

```bash
# 通过环境变量配置开发环境
NUXT_PORT=3000 nuxt dev
NUXT_HOST=0.0.0.0 nuxt dev

# 使用 .env 文件
NUXT_PUBLIC_API_URL=http://localhost:4000/api nuxt dev
```

### 模块管理

```bash
# 使用 CLI 添加模块（推荐）
npx nuxi module add @nuxt/icon
npx nuxi module add @pinia/nuxt

# 安装后自动更新 nuxt.config.ts
```

### 调试和诊断

```bash
# 启用详细日志
nuxt dev --logLevel verbose

# 分析构建
nuxt analyze

# 检查类型
npx nuxi typecheck

# 检查 Lint
npx nuxi lint
```

## Nuxi 命令

```bash
# 创建新项目
nuxi init

# 启动开发服务器
nuxi dev

# 构建
nuxi build

# 生成
nuxi generate

# 预览
nuxi preview
```

## 常见问题

### 端口被占用

```bash
# 自动使用下一个可用端口
nuxt dev --port 0

# 指定备用端口
nuxt dev --port 3001
```

### 开发服务器性能问题

```bash
# 禁用某些功能以提高开发速度
nuxt dev --no-clear
```

### 缓存问题

```bash
# 清理 .nuxt 缓存
nuxt clean

# 删除 node_modules/.cache
rm -rf node_modules/.cache
```

## CLI 命令速查表

| 命令 | 用途 | 常用选项 |
|------|------|----------|
| `nuxi init` | 创建新项目 | `--template`, `--packageManager` |
| `nuxt dev` | 启动开发服务器 | `--port`, `--host`, `--https`, `--tunnel` |
| `nuxt build` | 生产构建 | `--preset`, `--prerender` |
| `nuxt generate` | 生成静态站点 | - |
| `nuxt preview` | 预览生产构建 | `--port`, `--host` |
| `nuxi module add` | 添加模块 | - |
| `nuxt clean` | 清理缓存 | - |
| `nuxt analyze` | 分析包大小 | - |
| `nuxi typecheck` | 类型检查 | - |
| `nuxi lint` | ESLint 检查 | --fix |

<!-- 
源码参考：
- https://nuxt.com/docs/getting-started/installation
- https://nuxt.com/docs/api/commands/nuxt
- https://nuxt.com/docs/api/commands/nuxi
- https://nuxt.com/docs/api/commands/nuxi-module
-->