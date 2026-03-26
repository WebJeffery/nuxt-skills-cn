# turbo watch

完整文档：https://turborepo.dev/docs/reference/watch

代码更改时自动重新运行任务。感知依赖关系。

```bash
turbo watch [tasks]
```

## 基本用法

```bash
# 代码更改时监视并重新运行 build 任务
turbo watch build

# 监视多个任务
turbo watch build test lint
```

当源文件更改时，任务按照 `turbo.json` 中配置的顺序重新运行。

## 使用持久任务

持久任务（`"persistent": true`）不会退出，因此不能被依赖。它们在 `turbo watch` 中的工作方式与 `turbo run` 相同。

### 感知依赖关系的持久任务

如果您的工具具有内置监视（如 `next dev`），请使用其监视器：

```json
{
  "tasks": {
    "dev": {
      "persistent": true,
      "cache": false
    }
  }
}
```

### 非感知依赖关系的工具

对于不检测依赖关系更改的工具，使用 `interruptible`：

```json
{
  "tasks": {
    "dev": {
      "persistent": true,
      "interruptible": true,
      "cache": false
    }
  }
}
```

当依赖关系更改时，`turbo watch` 将重新启动可中断的任务。

## 限制

### 缓存

监视模式的缓存是实验性的：

```bash
turbo watch your-tasks --experimental-write-cache
```

### 源代码控制中的任务输出

如果任务写入 git 跟踪的文件，监视模式可能会无限循环。监视模式使用文件哈希来防止这种情况，但这并不完美。

**建议**：从 git 中删除任务输出。

## vs turbo run

| 特性           | `turbo run` | `turbo watch` |
| ----------------- | ----------- | ------------- |
| 运行一次         | 是         | 否            |
| 更改时重新运行 | 否          | 是           |
| 缓存           | 完整        | 实验性  |
| 用例          | CI、一次性 | 开发   |

## 常见模式

### 开发工作流

```bash
# 运行开发服务器并监视构建更改
turbo watch dev build
```

### 开发期间类型检查

```bash
# 监视并重新运行类型检查
turbo watch check-types
```