---
name: pnpm-store
description: 使 pnpm 快速且节省磁盘空间的内容寻址存储系统
---

# pnpm 存储

pnpm 使用内容寻址存储来节省磁盘空间并加速安装。所有包在全局存储一次，并硬链接到项目的 `node_modules`。

## 工作原理

1. **全局存储**：包下载一次到中央存储
2. **硬链接**：项目链接到存储而不是复制文件
3. **内容寻址**：文件按内容哈希存储，去重相同文件

### 存储布局

```
~/.pnpm-store/              # 全局存储（默认位置）
└── v3/
    └── files/
        └── <hash>/         # 按内容哈希存储的文件

project/
└── node_modules/
    ├── .pnpm/              # 虚拟存储（到全局存储的硬链接）
    │   ├── lodash@4.17.21/
    │   │   └── node_modules/
    │   │       └── lodash/
    │   └── express@4.18.2/
    │       └── node_modules/
    │           ├── express/
    │           └── <deps>/  # 依赖的扁平结构
    ├── lodash -> .pnpm/lodash@4.17.21/node_modules/lodash
    └── express -> .pnpm/express@4.18.2/node_modules/express
```

## 存储命令

```bash
# 显示存储位置
pnpm store path

# 删除未引用的包
pnpm store prune

# 检查存储完整性
pnpm store status

# 将包添加到存储而不安装
pnpm store add <pkg>
```

## 配置

### 存储位置

```ini
# .npmrc
store-dir=~/.pnpm-store

# 或使用环境变量
PNPM_HOME=~/.local/share/pnpm
```

### 虚拟存储

虚拟存储（`node_modules` 中的 `.pnpm`）包含到全局存储的符号链接：

```ini
# 自定义虚拟存储位置
virtual-store-dir=node_modules/.pnpm

# 替代扁平布局
node-linker=hoisted
```

## 磁盘空间优势

pnpm 节省大量磁盘空间：

- **去重**：相同包版本在所有项目中存储一次
- **内容去重**：不同包中的相同文件存储一次
- **硬链接**：不复制，只是链接

### 检查磁盘使用情况

```bash
# 比较实际大小与显示大小
du -sh node_modules        # 显示大小
du -sh --apparent-size node_modules  # 计算硬链接
```

## Node 链接器模式

配置 `node_modules` 的结构：

```ini
# 默认：符号链接结构（推荐）
node-linker=isolated

# 扁平 node_modules（类似 npm，用于兼容性）
node-linker=hoisted

# PnP 模式（实验性，类似 Yarn PnP）
node-linker=pnp
```

### 隔离模式（默认）

- 严格的依赖解析
- 没有幽灵依赖
- 包只能访问声明的依赖

### 提升模式

- 类似 npm 的扁平 `node_modules`
- 用于不支持符号链接的工具的兼容性
- 失去严格性优势

## 副作用缓存

缓存原生模块的构建输出：

```ini
# 启用副作用缓存
side-effects-cache=true

# 在项目中存储副作用（而不是全局存储）
side-effects-cache-readonly=true
```

## 跨机器共享存储

对于 CI/CD，您可以共享存储：

```yaml
# GitHub Actions 示例
- uses: pnpm/action-setup@v4
  with:
    run_install: false

- name: Get pnpm store directory
  shell: bash
  run: echo "STORE_PATH=$(pnpm store path --silent)" >> $GITHUB_ENV

- uses: actions/cache@v4
  with:
    path: ${{ env.STORE_PATH }}
    key: ${{ runner.os }}-pnpm-store-${{ hashFiles('**/pnpm-lock.yaml') }}
```

## 故障排除

### 存储损坏
```bash
# 验证并修复存储
pnpm store status
pnpm store prune
```

### 硬链接问题（网络驱动器、Docker）
```ini
# 使用复制而不是硬链接
package-import-method=copy
```

### 权限问题
```bash
# 修复存储权限
chmod -R u+w ~/.pnpm-store
```

<!--
源引用:
- https://pnpm.io/symlinked-node-modules-structure
- https://pnpm.io/cli/store
- https://pnpm.io/npmrc#store-dir
-->
