# 环境模式

Turborepo 支持在任务执行期间处理环境变量的不同模式。

## 严格模式（默认）

只有显式配置的变量可用于任务。

**行为：**

- 任务仅看到在 `env`、`globalEnv`、`passThroughEnv` 或 `globalPassThroughEnv` 中列出的变量
- 未列出的变量被过滤掉
- 如果任务需要未列出的变量，任务会失败

**优势：**

- 保证缓存正确性
- 防止对系统变量的意外依赖
- 跨机器的可重现构建

```bash
# 显式（虽然是默认值）
turbo run build --env-mode=strict
```

## 宽松模式

所有系统环境变量可用于任务。

```bash
turbo run build --env-mode=loose
```

**行为：**

- 每个系统环境变量都被传递
- 只有 `env`/`globalEnv` 中的变量影响哈希
- 其他变量可用但不被哈希

**风险：**

- 如果未哈希的变量更改，缓存可能会恢复不正确的结果
- "在我的机器上可以工作"错误
- CI 与本地环境不匹配

**使用情况：** 迁移旧项目或调试严格模式问题。

## 框架推断（自动）

Turborepo 自动检测框架并包含其常规环境变量。

### 按框架推断的变量

| 框架        | 模式             |
| ---------------- | ------------------- |
| Next.js          | `NEXT_PUBLIC_*`     |
| Vite             | `VITE_*`            |
| Create React App | `REACT_APP_*`       |
| Gatsby           | `GATSBY_*`          |
| Nuxt             | `NUXT_*`、`NITRO_*` |
| Expo             | `EXPO_PUBLIC_*`     |
| Astro            | `PUBLIC_*`          |
| SvelteKit        | `PUBLIC_*`          |
| Remix            | `REMIX_*`           |
| Redwood          | `REDWOOD_ENV_*`     |
| Sanity           | `SANITY_STUDIO_*`   |
| Solid            | `VITE_*`            |

### 禁用框架推断

通过 CLI 全局禁用：

```bash
turbo run build --framework-inference=false
```

或在配置中排除特定模式：

```json
{
  "tasks": {
    "build": {
      "env": ["!NEXT_PUBLIC_*"]
    }
  }
}
```

### 为什么要禁用？

- 你想要对所有环境变量进行显式控制
- 框架变量不应该破坏缓存（例如，分析 ID）
- 调试意外的缓存未命中

## 检查环境模式

使用 `--dry` 查看哪些变量影响每个任务：

```bash
turbo run build --dry=json | jq '.tasks[].environmentVariables'
```