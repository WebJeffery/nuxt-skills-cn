---
description: 加载 Turborepo 技能，用于在 monorepos 中创建工作流、任务和管道。当用户要求"创建工作流"、"制作任务"、"生成管道"或设置构建编排时使用。
---

加载 Turborepo 技能并帮助进行 monorepo 任务编排：创建工作流、配置任务、设置管道和优化构建。

## 工作流程

### 步骤 1：加载 turborepo 技能

```
skill({ name: 'turborepo' })
```

### 步骤 2：从用户请求中识别任务类型

分析 $ARGUMENTS 以确定：

- **主题**：配置、缓存、过滤、环境、CI 或 CLI
- **任务类型**：新设置、调试、优化或实现

使用 SKILL.md 中的决策树选择相关的参考文件。

### 步骤 3：读取相关参考文件

根据任务类型，从 `references/<topic>/` 读取：

| 任务                 | 要读取的文件                                           |
| -------------------- | ------------------------------------------------------- |
| 配置 turbo.json | `configuration/RULE.md` + `configuration/tasks.md`      |
| 调试缓存问题   | `caching/gotchas.md`                                    |
| 设置远程缓存  | `caching/remote-cache.md`                               |
| 过滤包      | `filtering/RULE.md` + `filtering/patterns.md`           |
| 环境问题 | `environment/gotchas.md` + `environment/modes.md`       |
| 设置 CI            | `ci/RULE.md` + `ci/github-actions.md` 或 `ci/vercel.md` |
| CLI 使用            | `cli/commands.md`                                       |

### 步骤 4：执行任务

应用参考中的 Turborepo 特定模式来完成用户请求。

**关键 - 创建任务/脚本/管道时：**

1. **不要创建根任务** - 始终创建包任务
2. 将脚本添加到每个相关包的 `package.json`（例如 `apps/web/package.json`、`packages/ui/package.json`）
3. 在根 `turbo.json` 中注册任务
4. 根 `package.json` 仅包含 `turbo run <task>` - 绝不包含实际任务逻辑

**其他需要验证的事项：**

- 为可缓存任务定义了 `outputs`
- `dependsOn` 使用正确的语法（`^task` vs `task`）
- 环境变量在 `env` 键中
- `.env` 文件在 `inputs` 中（如果使用）
- 在 package.json 和 CI 中使用 `turbo run`（而不是 `turbo`）

### 步骤 5：总结

```
=== Turborepo 任务完成 ===

主题: <configuration|caching|filtering|environment|ci|cli>
参考文件: <已查阅的参考文件>

<简要总结所做的工作>
```

<user-request>
$ARGUMENTS
</user-request>
