# 调试缓存问题

## 诊断工具

### `--summarize`

生成包含所有哈希输入的 JSON 文件。比较两次运行以查找差异。

```bash
turbo build --summarize
# 创建 .turbo/runs/<run-id>.json
```

摘要包括：

- 全局哈希及其输入
- 每个任务的哈希及其输入
- 影响哈希的环境变量

**比较运行：**

```bash
# 运行两次，比较摘要
diff .turbo/runs/<first-run>.json .turbo/runs/<second-run>.json
```

### `--dry` / `--dry=json`

查看将要运行的内容而不执行任何操作：

```bash
turbo build --dry
turbo build --dry=json  # 机器可读输出
```

显示每个任务的缓存状态而不运行它们。

### `--force`

跳过读取缓存，重新执行所有任务：

```bash
turbo build --force
```

用于验证任务实际工作（不仅仅是缓存结果）。

## 意外的缓存未命中

**症状：** 当你预期缓存命中时任务运行。

### 环境变量改变

检查 `env` 键中的环境变量是否改变：

```json
{
  "tasks": {
    "build": {
      "env": ["API_URL", "NODE_ENV"]
    }
  }
}
```

运行之间不同的 `API_URL` = 缓存未命中。

### .env 文件改变

`.env` 文件默认不被跟踪。添加到 `inputs`：

```json
{
  "tasks": {
    "build": {
      "inputs": ["$TURBO_DEFAULT$", ".env", ".env.local"]
    }
  }
}
```

或对仓库范围的 env 文件使用 `globalDependencies`：

```json
{
  "globalDependencies": [".env"]
}
```

### Lockfile 改变

安装/更新包会改变全局哈希。

### 源文件改变

包中的任何文件（或在 `inputs` 中）都会触发未命中。

### turbo.json 改变

配置更改会使全局哈希失效。

## 不正确的缓存命中

**症状：** 缓存的输出是过时的/错误的。

### 缺少环境变量

任务使用了未在 `env` 中列出的环境变量：

```javascript
// build.js
const apiUrl = process.env.API_URL; // 未跟踪！
```

修复：添加到任务配置：

```json
{
  "tasks": {
    "build": {
      "env": ["API_URL"]
    }
  }
}
```

### 输入中缺少文件

任务读取默认输入之外的文件：

```json
{
  "tasks": {
    "build": {
      "inputs": [
        "$TURBO_DEFAULT$",
        "../../shared-config.json" // 包外的文件
      ]
    }
  }
}
```

## 有用的标志

```bash
# 仅显示缓存未命中的输出
turbo build --output-logs=new-only

# 显示所有内容的输出（调试）
turbo build --output-logs=full

# 查看任务为什么运行
turbo build --verbosity=2
```

## 快速检查清单

预期命中但缓存未命中：

1. 使用 `--summarize` 运行，与之前的运行比较
2. 使用 `--dry=json` 检查环境变量
3. 在 git 中查找 lockfile/配置更改

预期未命中但缓存命中：

1. 验证环境变量在 `env` 数组中
2. 验证文件在 `inputs` 数组中
3. 检查文件是否在包目录之外