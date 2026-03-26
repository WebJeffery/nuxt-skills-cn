---
name: develop-guide
description: Anthony Fu 对 JavaScript/TypeScript 项目的工具和约定。用于设置新项目、配置 ESLint/Prettier 替代方案、monorepo、库发布，或当用户提到 Anthony Fu 的偏好时。
metadata:
  author: Anthony Fu
  version: "2026.02.03"
---

## 编码实践

### 代码组织

- **单一职责**：每个源文件应具有清晰、专注的范围/目的
- **拆分大文件**：当文件变大或处理过多关注点时拆分文件
- **类型分离**：始终将类型和接口分离到 `types.ts` 或 `types/*.ts` 中
- **常量提取**：将常量移动到专用的 `constants.ts` 文件中

### 运行时环境

- **优先使用同构代码**：尽可能编写与运行时无关的代码，使其在 Node、浏览器和 worker 中工作
- **清晰的运行时指示器**：当代码特定于环境时，在文件顶部添加注释：

```ts
// @env node
// @env browser
```

### TypeScript

- **显式返回类型**：尽可能显式声明返回类型
- **避免复杂的内联类型**：将复杂类型提取到专用的 `type` 或 `interface` 声明中

### 注释

- **避免不必要的注释**：代码应自我解释
- **解释"为什么"而不是"如何"**：注释应描述推理或意图，而不是代码做什么

### 测试 (Vitest)

- 测试文件：`foo.ts` → `foo.test.ts`（同一目录）
- 使用 `describe`/`it` API（而不是 `test`）
- 对复杂输出使用 `toMatchSnapshot`
- 对特定语言的快照使用 `toMatchFileSnapshot` 和显式路径

---

## 工具选择

### @antfu/ni 命令

| 命令 | 描述 |
|---------|-------------|
| `ni` | 安装依赖 |
| `ni <pkg>` / `ni -D <pkg>` | 添加依赖 / 开发依赖 |
| `nr <script>` | 运行脚本 |
| `nu` | 升级依赖 |
| `nun <pkg>` | 卸载依赖 |
| `nci` | 清洁安装（`pnpm i --frozen-lockfile`） |
| `nlx <pkg>` | 执行包（`npx`） |

### TypeScript 配置

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true
  }
}
```

### ESLint 设置

```js
// eslint.config.mjs
import antfu from '@antfu/eslint-config'

export default antfu()
```

完成任务时，运行 `pnpm run lint --fix` 以格式化代码并修复编码风格。

有关详细配置选项：[antfu-eslint-config](references/antfu-eslint-config.md)

### Git Hooks

```json
{
  "simple-git-hooks": {
    "pre-commit": "pnpm i --frozen-lockfile --ignore-scripts --offline && npx lint-staged"
  },
  "lint-staged": { "*": "eslint --fix" },
  "scripts": {
    "prepare": "npx simple-git-hooks"
  }
}
```

### pnpm Catalogs

在 `pnpm-workspace.yaml` 中使用命名目录进行版本管理：

| 目录 | 用途 |
|---------|---------|
| `prod` | 生产依赖 |
| `inlined` | 打包器内联依赖 |
| `dev` | 开发工具（linter、打包器、测试） |
| `frontend` | 前端库 |

避免使用默认目录。目录名称可以根据项目需求进行调整。

---

## 参考

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| ESLint 配置 | 框架支持、格式化程序、规则覆盖、VS Code 设置 | [antfu-eslint-config](references/antfu-eslint-config.md) |
| 项目设置 | .gitignore、GitHub Actions、VS Code 扩展 | [setting-up](references/setting-up.md) |
| 应用开发 | Vue/Nuxt/UnoCSS 约定和模式 | [app-development](references/app-development.md) |
| 库开发 | tsdown 打包、纯 ESM 发布 | [library-development](references/library-development.md) |
| Monorepo | pnpm 工作区、集中别名、Turborepo | [monorepo](references/monorepo.md) |
