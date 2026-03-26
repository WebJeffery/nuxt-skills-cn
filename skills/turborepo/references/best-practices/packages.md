# 创建内部包

如何在 monorepo 中创建和构建内部包。

## 包创建清单

1. 在 `packages/` 中创建目录
2. 添加带有名称和导出的 `package.json`
3. 在 `src/` 中添加源代码
4. 如果使用 TypeScript，添加 `tsconfig.json`
5. 在使用包中作为依赖安装
6. 运行包管理器安装以更新锁文件

## 包编译策略

### 即时编译（JIT）

直接导出 TypeScript。使用它的应用的打包器编译它。

```json
// packages/ui/package.json
{
  "name": "@repo/ui",
  "exports": {
    "./button": "./src/button.tsx",
    "./card": "./src/card.tsx"
  },
  "scripts": {
    "lint": "eslint .",
    "check-types": "tsc --noEmit"
  }
}
```

**何时使用：**

- 应用使用现代打包器（Turbopack、webpack、Vite）
- 您想要最少的配置
- 没有缓存的情况下构建时间可接受

**限制：**

- 包本身没有 Turborepo 缓存
- 使用者必须支持 TypeScript 编译
- 不能使用 TypeScript `paths`（改用 Node.js 子路径导入）

### 编译

包处理自己的编译。

```json
// packages/ui/package.json
{
  "name": "@repo/ui",
  "exports": {
    "./button": {
      "types": "./src/button.tsx",
      "default": "./dist/button.js"
    }
  },
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch"
  }
}
```

```json
// packages/ui/tsconfig.json
{
  "extends": "@repo/typescript-config/library.json",
  "compilerOptions": {
    "outDir": "dist",
    "rootDir": "src"
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

**何时使用：**

- 您希望 Turborepo 缓存构建
- 包将被非打包器工具使用
- 您需要最大兼容性

**记住：** 将 `dist/**` 添加到 turbo.json 输出中！

## 定义导出

### 多个入口点

```json
{
  "exports": {
    ".": "./src/index.ts", // @repo/ui
    "./button": "./src/button.tsx", // @repo/ui/button
    "./card": "./src/card.tsx", // @repo/ui/card
    "./hooks": "./src/hooks/index.ts" // @repo/ui/hooks
  }
}
```

### 条件导出（编译）

```json
{
  "exports": {
    "./button": {
      "types": "./src/button.tsx",
      "import": "./dist/button.mjs",
      "require": "./dist/button.cjs",
      "default": "./dist/button.js"
    }
  }
}
```

## 安装内部包

### 添加到使用包

```json
// apps/web/package.json
{
  "dependencies": {
    "@repo/ui": "workspace:*" // pnpm/bun
    // "@repo/ui": "*"         // npm/yarn
  }
}
```

### 运行安装

```bash
pnpm install  # 使用新依赖更新锁文件
```

### 导入和使用

```typescript
// apps/web/src/page.tsx
import { Button } from '@repo/ui/button';

export default function Page() {
  return <Button>Click me</Button>;
}
```

## 每个包一个目的

### 好的示例

```
packages/
├── ui/                  # 共享 UI 组件
├── utils/               # 通用工具
├── auth/                # 身份验证逻辑
├── database/            # 数据库客户端/模式
├── eslint-config/       # ESLint 配置
├── typescript-config/   # TypeScript 配置
└── api-client/          # 生成的 API 客户端
```

### 避免巨型包

```
// 错误：一个包用于所有内容
packages/
└── shared/
    ├── components/
    ├── utils/
    ├── hooks/
    ├── types/
    └── api/

// 好：按目的分离
packages/
├── ui/          # 组件
├── utils/       # 工具
├── hooks/       # React 钩子
├── types/       # 共享 TypeScript 类型
└── api-client/  # API 工具
```

## 配置包

### TypeScript 配置

```json
// packages/typescript-config/package.json
{
  "name": "@repo/typescript-config",
  "exports": {
    "./base.json": "./base.json",
    "./nextjs.json": "./nextjs.json",
    "./library.json": "./library.json"
  }
}
```

### ESLint 配置

```json
// packages/eslint-config/package.json
{
  "name": "@repo/eslint-config",
  "exports": {
    "./base": "./base.js",
    "./next": "./next.js"
  },
  "dependencies": {
    "eslint": "^8.0.0",
    "eslint-config-next": "latest"
  }
}
```

## 常见错误

### 忘记导出

```json
// 错误：没有定义导出
{
  "name": "@repo/ui"
}

// 好：清晰的导出
{
  "name": "@repo/ui",
  "exports": {
    "./button": "./src/button.tsx"
  }
}
```

### 错误的工作区语法

```json
// pnpm/bun
{ "@repo/ui": "workspace:*" }  // 正确

// npm/yarn
{ "@repo/ui": "*" }            // 正确
{ "@repo/ui": "workspace:*" }  // npm/yarn 错误！
```

### 缺少 turbo.json 输出

```json
// 包构建到 dist/，但 turbo.json 不知道
{
  "tasks": {
    "build": {
      "outputs": [".next/**"]  // 缺少 dist/**！
    }
  }
}

// 正确
{
  "tasks": {
    "build": {
      "outputs": [".next/**", "dist/**"]
    }
  }
}
```

## TypeScript 最佳实践

### 使用 Node.js 子路径导入（不是 `paths`）

TypeScript `compilerOptions.paths` 在 JIT 包中会中断。改用 Node.js 子路径导入（TypeScript 5.4+）。

**JIT 包：**

```json
// packages/ui/package.json
{
  "imports": {
    "#*": "./src/*"
  }
}
```

```typescript
// packages/ui/button.tsx
import { MY_STRING } from "#utils.ts"; // 使用 .ts 扩展名
```

**编译包：**

```json
// packages/ui/package.json
{
  "imports": {
    "#*": "./dist/*"
  }
}
```

```typescript
// packages/ui/button.tsx
import { MY_STRING } from "#utils.js"; // 使用 .js 扩展名
```

### 对内部包使用 `tsc`

对于内部包，优先使用 `tsc` 而不是打包器。打包器可能会在代码到达应用的打包器之前对其进行混淆，导致难以调试的问题。

### 启用转到定义

对于编译包，启用声明映射：

```json
// tsconfig.json
{
  "compilerOptions": {
    "declaration": true,
    "declarationMap": true
  }
}
```

这会创建 `.d.ts` 和 `.d.ts.map` 文件用于 IDE 导航。

### 不需要根 tsconfig.json

每个包应该有自己的 `tsconfig.json`。根目录的 tsconfig.json 会在更改时导致所有任务缓存未命中。仅对非包脚本使用根 `tsconfig.json`。

### 避免 TypeScript 项目引用

它们增加了复杂性和另一个缓存层。Turborepo 更好地处理依赖关系。
