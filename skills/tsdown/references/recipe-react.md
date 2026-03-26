# React 支持

使用 tsdown 构建 React 组件库。

## 概述

tsdown 为 React 库提供一流的支持。Rolldown 原生支持 JSX/TSX，因此基本的 React 组件不需要额外的插件。

## 快速开始

### 使用启动模板

```bash
# 基本 React 库
npx create-tsdown@latest -t react

# 使用 React Compiler
npx create-tsdown@latest -t react-compiler
```

## 基本配置

### 最小设置

```ts
// tsdown.config.ts
export default defineConfig({
  entry: ['./src/index.ts'],
  format: ['esm', 'cjs'],
  platform: 'neutral',
  deps: {
    neverBundle: ['react', 'react-dom'],
  },
  dts: true,
})
```

### 组件示例

```tsx
// src/MyButton.tsx
import React from 'react'

interface MyButtonProps {
  type?: 'primary' | 'secondary'
  onClick?: () => void
}

export const MyButton: React.FC<MyButtonProps> = ({ type = 'primary', onClick }) => {
  return (
    <button className={`btn btn-${type}`} onClick={onClick}>
      Click me
    </button>
  )
}
```

```ts
// src/index.ts
export { MyButton } from './MyButton'
```

## JSX 转换

### 自动转换（默认）

现代 JSX 转换（React 17+）：

```ts
export default defineConfig({
  entry: ['src/index.tsx'],
  // 自动 JSX 是默认的
})
```

**特点：**
- 不需要 `import React`
- 更小的打包体积
- 需要 React 17+

### 经典转换

传统 JSX 转换：

```ts
export default defineConfig({
  entry: ['src/index.tsx'],
  inputOptions: {
    transform: {
      jsx: 'react',  // 经典转换
    },
  },
})
```

**特点：**
- 需要 `import React from 'react'`
- 与旧版本 React 兼容

## React Compiler

React Compiler 在构建时自动优化 React 代码。

### 安装依赖

```bash
pnpm add -D @rollup/plugin-babel babel-plugin-react-compiler
```

### 配置

```ts
import pluginBabel from '@rollup/plugin-babel'

export default defineConfig({
  entry: ['src/index.tsx'],
  format: ['esm', 'cjs'],
  deps: {
    neverBundle: ['react', 'react-dom'],
  },
  plugins: [
    pluginBabel({
      babelHelpers: 'bundled',
      parserOpts: {
        sourceType: 'module',
        plugins: ['jsx', 'typescript'],
      },
      plugins: ['babel-plugin-react-compiler'],
      extensions: ['.js', '.jsx', '.ts', '.tsx'],
    }),
  ],
  dts: true,
})
```

## 常见模式

### 组件库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  platform: 'neutral',
  deps: {
    neverBundle: [
      'react',
      'react-dom',
      /^react\//,  // react/jsx-runtime 等。
    ],
  },
  dts: true,
  clean: true,
})
```

### 多个组件

```ts
export default defineConfig({
  entry: {
    index: 'src/index.ts',
    Button: 'src/Button.tsx',
    Input: 'src/Input.tsx',
    Modal: 'src/Modal.tsx',
  },
  format: ['esm', 'cjs'],
  deps: {
    neverBundle: ['react', 'react-dom'],
  },
  dts: true,
})
```

### Hooks 库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  platform: 'neutral',
  deps: {
    neverBundle: ['react'],  // 只需要 React
  },
  dts: true,
  treeshake: true,
})
```

### Monorepo React 包

```ts
export default defineConfig({
  workspace: 'packages/*',
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  deps: {
    neverBundle: [
      'react',
      'react-dom',
      /^@mycompany\//,  // 其他工作区包
    ],
  },
  dts: true,
})
```

## TypeScript 配置

### 推荐的 tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "jsx": "react-jsx",  // 或 "react" 用于经典模式
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "strict": true,
    "isolatedDeclarations": true,  // 快速 DTS 生成
    "skipLibCheck": true
  },
  "include": ["src"]
}
```

## Package.json 配置

```json
{
  "name": "my-react-library",
  "version": "1.0.0",
  "type": "module",
  "main": "./dist/index.cjs",
  "module": "./dist/index.mjs",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "types": "./dist/index.d.ts",
      "import": "./dist/index.mjs",
      "require": "./dist/index.cjs"
    }
  },
  "files": ["dist"],
  "peerDependencies": {
    "react": "^18.0.0",
    "react-dom": "^18.0.0"
  },
  "devDependencies": {
    "@types/react": "^18.0.0",
    "@types/react-dom": "^18.0.0",
    "react": "^18.0.0",
    "react-dom": "^18.0.0",
    "tsdown": "^0.9.0",
    "typescript": "^5.0.0"
  }
}
```

## 高级模式

### 使用 Fast Refresh（开发）

```ts
import react from '@vitejs/plugin-react'

export default defineConfig((options) => ({
  entry: ['src/index.ts'],
  format: ['esm'],
  deps: {
    neverBundle: ['react', 'react-dom'],
  },
  plugins: options.watch
    ? [
        // @ts-expect-error Vite 插件
        react({ fastRefresh: true }),
      ]
    : [],
}))
```

## 提示

1. **始终外部化 React** - 不要打包 React/ReactDOM
2. **使用自动 JSX** - React 17+ 更小的打包体积
3. **启用 DTS 生成** - TypeScript 支持必不可少
4. **使用 platform: 'neutral'** - 获得最大兼容性
5. **添加 peer dependencies** - 让用户提供 React
6. **启用 tree shaking** - 减少打包体积
7. **使用 React Compiler** - 更好的运行时性能

## 故障排除

### React Hook 错误

确保 React 已外部化：

```ts
deps: {
  neverBundle: ['react', 'react-dom', /^react\//],
}
```

### JSX 类型错误

检查 `tsconfig.json`：

```json
{
  "compilerOptions": {
    "jsx": "react-jsx"  // 或 "react"
  }
}
```

### 重复的 React

添加到 deps.neverBundle：

```ts
deps: {
  neverBundle: [
    'react',
    'react-dom',
    'react/jsx-runtime',
    'react/jsx-dev-runtime',
  ],
}
```

## 相关内容

- [插件](advanced-plugins.md) - 扩展功能
- [依赖](option-dependencies.md) - 外部包
- [DTS](option-dts.md) - 类型声明
- [Vue 配方](recipe-vue.md) - Vue 组件库
