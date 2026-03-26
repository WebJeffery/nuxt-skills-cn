---
name: pnpm-performance-optimization
description: 更快安装和更好性能的技巧和窍门
---

# pnpm 性能优化

pnpm 默认很快，但这些优化可以使其更快。

## 安装优化

### 使用冻结锁文件

当 lockfile 存在时跳过解析：

```bash
pnpm install --frozen-lockfile
```

这更快，因为 pnpm 完全跳过解析阶段。

### 优先离线模式

在可用时使用缓存的包：

```bash
pnpm install --prefer-offline
```

或全局配置：
```ini
# .npmrc
prefer-offline=true
```

### 跳过可选依赖

如果您不需要可选依赖：

```bash
pnpm install --no-optional
```

### 跳过脚本

对于 CI 或不需要脚本时：

```bash
pnpm install --ignore-scripts
```

**警告：** 某些包需要 postinstall 脚本才能正常工作。

### 仅构建特定依赖

仅对特定包运行构建脚本：

```ini
# .npmrc
onlyBuiltDependencies[]=esbuild
onlyBuiltDependencies[]=sharp
onlyBuiltDependencies[]=@swc/core
```

或跳过不需要构建的依赖的构建：

```json
{
  "pnpm": {
    "neverBuiltDependencies": ["fsevents", "cpu-features"]
  }
}
```

## 存储优化

### 副作用缓存

缓存原生模块的构建输出：

```ini
# .npmrc
side-effects-cache=true
```

这会缓存 postinstall 脚本的结果，加快后续安装。

### 共享存储

为所有项目使用单个存储（默认行为）：

```ini
# .npmrc
store-dir=~/.pnpm-store
```

优势：
- 包为所有项目下载一次
- 硬链接节省磁盘空间
- 从缓存更快安装

### 存储维护

定期清理未使用的包：

```bash
# 删除未引用的包
pnpm store prune

# 检查存储完整性
pnpm store status
```

## 工作区优化

### 并行执行

并行运行工作区脚本：

```bash
pnpm -r --parallel run build
```

控制并发：
```ini
# .npmrc
workspace-concurrency=8
```

### 流式输出

实时查看输出：

```bash
pnpm -r --stream run build
```

### 过滤到更改的包

仅构建更改的内容：

```bash
# 构建自 main 分支以来更改的包
pnpm --filter "...[origin/main]" run build
```

### 拓扑顺序

在依赖者之前构建依赖：

```bash
pnpm -r run build
# 自动按拓扑顺序运行
```

对于显式顺序构建：
```bash
pnpm -r --workspace-concurrency=1 run build
```

## 网络优化

### 配置注册表

使用最近/最快的注册表：

```ini
# .npmrc
registry=https://registry.npmmirror.com/
```

### HTTP 设置

调整网络设置：

```ini
# .npmrc
fetch-retries=3
fetch-retry-mintimeout=10000
fetch-retry-maxtimeout=60000
network-concurrency=16
```

### 代理配置

```ini
# .npmrc
proxy=http://proxy.company.com:8080
https-proxy=http://proxy.company.com:8080
```

## 锁文件优化

### 单个锁文件（Monorepos）

为所有包使用共享锁文件（默认）：

```ini
# .npmrc
shared-workspace-lockfile=true
```

优势：
- 单一事实来源
- 更快解析
- 工作区中一致的版本

### 仅锁文件模式

仅更新 lockfile 而不安装：

```bash
pnpm install --lockfile-only
```

## 基准测试

### 比较安装时间

```bash
# 清洁安装
rm -rf node_modules pnpm-lock.yaml
time pnpm install

# 缓存安装（有 lockfile）
rm -rf node_modules
time pnpm install --frozen-lockfile

# 有存储缓存
time pnpm install --frozen-lockfile --prefer-offline
```

### 分析解析

调试慢安装：

```bash
# 详细日志
pnpm install --reporter=append-only

# 调试模式
DEBUG=pnpm:* pnpm install
```

## 配置总结

针对性能优化的 `.npmrc`：

```ini
# 安装行为
prefer-offline=true
auto-install-peers=true

# 构建优化
side-effects-cache=true
# 仅构建必要的内容
onlyBuiltDependencies[]=esbuild
onlyBuiltDependencies[]=@swc/core

# 网络
fetch-retries=3
network-concurrency=16

# 工作区
workspace-concurrency=4
```

## 快速参考

| 场景 | 命令/设置 |
|----------|-----------------|
| CI 安装 | `pnpm install --frozen-lockfile` |
| 离线开发 | `--prefer-offline` |
| 跳过原生构建 | `neverBuiltDependencies` |
| 并行工作区 | `pnpm -r --parallel run build` |
| 仅构建更改 | `pnpm --filter "...[origin/main]" build` |
| 清洁存储 | `pnpm store prune` |

<!--
源引用:
- https://pnpm.io/npmrc
- https://pnpm.io/cli/install
- https://pnpm.io/filtering
-->
