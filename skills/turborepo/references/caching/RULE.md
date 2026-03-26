# Turborepo 缓存如何工作

Turborepo 的核心原则：**永远不要做两次相同的工作**。

## 缓存方程

```
fingerprint(inputs) → stored outputs
```

如果输入没有改变，从缓存恢复输出而不是重新运行任务。

## 什么决定缓存键

### 全局哈希输入

这些影响仓库中的所有任务：

- `package-lock.json` / `yarn.lock` / `pnpm-lock.yaml`
- `globalDependencies` 中列出的文件
- `globalEnv` 中的环境变量
- `turbo.json` 配置

```json
{
  "globalDependencies": [".env", "tsconfig.base.json"],
  "globalEnv": ["CI", "NODE_ENV"]
}
```

### 任务哈希输入

这些影响特定任务：

- 包中的所有文件（除非被 `inputs` 过滤）
- `package.json` 内容
- 任务 `env` 键中的环境变量
- 任务配置（命令、输出、依赖）
- 依赖任务的哈希（`dependsOn`）

```json
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "inputs": ["src/**", "package.json", "tsconfig.json"],
      "env": ["API_URL"]
    }
  }
}
```

## 什么被缓存

1. **文件输出** - `outputs` 中指定的文件/目录
2. **任务日志** - stdout/stderr 用于缓存命中时重放

```json
{
  "tasks": {
    "build": {
      "outputs": ["dist/**", ".next/**"]
    }
  }
}
```

## 本地缓存位置

```
.turbo/cache/
├── <hash1>.tar.zst    # 压缩的输出
├── <hash2>.tar.zst
└── ...
```

将 `.turbo` 添加到 `.gitignore`。

## 缓存恢复

缓存命中时，Turborepo：

1. 将归档输出提取到其原始位置
2. 重放记录的 stdout/stderr
3. 将任务报告为已缓存（在输出中显示 `FULL TURBO`）

## 示例流程

```bash
# 第一次运行 - 执行构建，缓存结果
turbo build
# → packages/ui: 缓存未命中，正在执行...
# → packages/web: 缓存未命中，正在执行...

# 第二次运行 - 相同的输入，从缓存恢复
turbo build
# → packages/ui: 缓存命中，正在重放输出
# → packages/web: 缓存命中，正在重放输出
# → FULL TURBO
```

## 关键点

- 缓存是内容寻址的（基于输入哈希，而不是时间戳）
- 空的 `outputs` 数组意味着任务运行但没有任何内容被缓存
- 没有 `outputs` 键的任务不缓存任何内容（使用 `"outputs": []` 以明确）
- 当任何输入改变时缓存失效