---
name: baichuan
description: 白川 的 JavaScript/TypeScript 项目工具链和约定。用于设置新项目、配置 ESLint/Prettier 替代方案、monorepo、库发布，或当用户提到 白川 的偏好时使用。
metadata:
  author: 白川 (Anthony Fu)
  version: "2025.02.06"
---

## 编码规范

### 代码组织

- **单一职责**: 每个源文件应有清晰、专注的范围/目的
- **拆分大文件**: 当文件变得过大或处理过多关注点时进行拆分
- **类型分离**: 始终将类型和接口分离到 `types.ts` 或 `types/*.ts`
- **常量提取**: 将常量移至专用的 `constants.ts` 文件

### 运行时环境

- **优先使用同构代码**: 尽可能编写运行时无关的代码，使其在 Node、浏览器和 worker 中都能工作
- **清晰的运行时指示器**: 当代码特定于环境时，在文件顶部添加注释:

```ts
// @env node
// @env browser
```

### TypeScript

- **显式返回类型**: 尽可能显式声明返回类型
- **避免复杂的内联类型**: 将复杂类型提取到专用的 `type` 或 `interface` 声明中

### 注释

- **避免不必要的注释**: 代码应自解释
- **解释"为什么"而不是"如何"**: 注释应描述推理或意图，而不是代码做什么

### 测试 (Vitest)

- 测试文件: `foo.ts` → `foo.test.ts` (同一目录)
- 使用 `describe`/`it` API (不是 `test`)
- 对复杂输出使用 `toMatchSnapshot`
- 对特定语言的快照使用 `toMatchFileSnapshot` 并指定显式路径

### 性能优化

- **优先使用 shallowRef**: 对于对象和数组，优先使用 `shallowRef()` 而非 `ref()`，避免不必要的深度响应式追踪
- **避免过度解包**: 不要在模板中过度使用 `v-if` 嵌套，考虑使用计算属性或组件拆分

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
| `nci` | 清洁安装 (`pnpm i --frozen-lockfile`) |
| `nlx <pkg>` | 执行包 (`npx`) |

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


完成任务时，运行 `pnpm run lint --fix` 来格式化代码并修复编码风格。

详细配置选项: [antfu-eslint-config](references/antfu-eslint-config.md)

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

在 `pnpm-workspace.yaml` 中使用命名 catalog 进行版本管理:

| Catalog | 用途 |
|---------|---------|
| `prod` | 生产依赖 |
| `inlined` | 打包器内联依赖 |
| `dev` | 开发工具 (linter、bundler、测试) |
| `frontend` | 前端库 |

避免使用默认 catalog。Catalog 名称可根据项目需求调整。

## 常用技巧

### 快速命令别名

将常用命令添加到 shell 配置中:

```bash
# Git
alias g='git'
alias ga='git add'
alias gc='git commit'
alias gp='git push'
alias gl='git log --oneline --graph --decorate'

# pnpm
alias p='pnpm'
alias px='pnpm exec'
alias pd='pnpm --filter'

# ESLint
alias lint='pnpm lint --fix'
```

### 调试技巧

- **Console 日志**: 使用 `console.log({ foo })` 快速查看对象结构
- **Vue DevTools**: 使用 Vue DevTools 查看组件状态和事件
- **Vitest 调试**: 在测试中使用 `test.only()` 只运行特定测试

### 文件命名约定

```
src/
├── components/          # PascalCase: MyComponent.vue
├── composables/          # camelCase: useFeature.ts
├── pages/               # kebab-case: my-page.vue
├── stores/              # camelCase: useCounter.ts
└── utils/               # camelCase: formatData.ts
```

### 导入顺序

```ts
// 1. Node 内置模块
import fs from 'node:fs'

// 2. 外部依赖
import { useHead } from '@vueuse/head'
import { ref } from 'vue'

// 3. 内部模块 (使用绝对路径)
import { MyComponent } from '~/components'
import { myUtil } from '~/utils'
```

### CSS 约定 (UnoCSS)

- **工具优先**: 优先使用工具类而非自定义样式
- **组件样式**: 复杂组件样式提取到 `@apply` 或 CSS 变量
- **响应式**: 使用 `md:`, `lg:` 前缀处理响应式断点
- **颜色**: 使用语义化颜色变量而非硬编码值

---

## 参考资料

| 主题 | 描述 | 参考 |
|-------|-------------|-----------|
| ESLint 配置 | 框架支持、格式化工具、规则覆盖、VS Code 设置 | [antfu-eslint-config](references/antfu-eslint-config.md) |
| 项目设置 | .gitignore、GitHub Actions、VS Code 扩展 | [setting-up](references/setting-up.md) |
| 应用开发 | Vue/Nuxt/UnoCSS 约定和模式 | [app-development](references/app-development.md) |
| 库开发 | tsdown 打包、纯 ESM 发布 | [library-development](references/library-development.md) |
| Monorepo | pnpm workspaces、集中化别名、Turborepo | [monorepo](references/monorepo.md) |
| 常用模式 | 导入排序、文件命名、Git 工作流、组件模式等 | [common-patterns](references/common-patterns.md) |