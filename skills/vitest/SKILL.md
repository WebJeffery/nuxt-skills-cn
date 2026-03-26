---
name: vitest
description: Vitest 是由 Vite 驱动的快速单元测试框架,具有 Jest 兼容的 API。在编写测试、模拟、配置覆盖率或使用测试过滤和 fixtures 时使用。
---

Vitest 是由 Vite 驱动的下一代测试框架。它提供 Jest 兼容的 API,开箱即支持原生 ESM、TypeScript 和 JSX。Vitest 与您的 Vite 应用共享相同的配置、转换器、解析器和插件。

**关键功能:**
- Vite 原生: 使用 Vite 的转换管道进行类似 HMR 的快速测试更新
- Jest 兼容: 大多数 Jest 测试套件的直接替代品
- 智能监视模式: 仅基于模块图重新运行受影响的测试
- 原生 ESM、TypeScript、JSX 支持无需配置
- 多线程工作器用于并行测试执行
- 通过 V8 或 Istanbul 内置覆盖率
- 快照测试、模拟和 spy 工具

> 该技能基于 Vitest 3.x,生成于 2026-01-28。

## 核心

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 配置 | Vitest 和 Vite 配置集成、defineConfig 用法 | [core-config](references/core-config.md) |
| CLI | 命令行界面、命令和选项 | [core-cli](references/core-cli.md) |
| 测试 API | test/it 函数、skip、only、concurrent 等修饰符 | [core-test-api](references/core-test-api.md) |
| Describe API | describe/suite 用于分组测试和嵌套套件 | [core-describe](references/core-describe.md) |
| Expect API | 断言,包括 toBe、toEqual、matchers 和非对称 matchers | [core-expect](references/core-expect.md) |
| Hooks | beforeEach、afterEach、beforeAll、afterAll、aroundEach | [core-hooks](references/core-hooks.md) |

## 功能

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| 模拟 | 使用 vi 工具模拟函数、模块、计时器、日期 | [features-mocking](references/features-mocking.md) |
| 快照 | 使用 toMatchSnapshot 和内联快照进行快照测试 | [features-snapshots](references/features-snapshots.md) |
| 覆盖率 | 使用 V8 或 Istanbul 提供程序的代码覆盖率 | [features-coverage](references/features-coverage.md) |
| 测试上下文 | 测试 fixtures、context.expect、test.extend 用于自定义 fixtures | [features-context](references/features-context.md) |
| 并发 | 并发测试、并行执行、分片 | [features-concurrency](references/features-concurrency.md) |
| 过滤 | 按名称、文件模式、标签过滤测试 | [features-filtering](references/features-filtering.md) |

## 高级

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| Vi 工具 | vi 辅助: mock、spyOn、假计时器、hoisted、waitFor | [advanced-vi](references/advanced-vi.md) |
| 环境 | 测试环境: node、jsdom、happy-dom、自定义 | [advanced-environments](references/advanced-environments.md) |
| 类型测试 | 使用 expectTypeOf 和 assertType 进行类型级测试 | [advanced-type-testing](references/advanced-type-testing.md) |
| 项目 | 多项目工作区、每个项目不同配置 | [advanced-projects](references/advanced-projects.md) |
