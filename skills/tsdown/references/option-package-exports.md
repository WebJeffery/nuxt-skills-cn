# 自动生成包导出

根据构建输出自动生成 package.json exports 字段。

## 概述

tsdown 可以根据您的构建输出自动推断和生成 `package.json` 中的 `exports`、`main`、`module` 和 `types` 字段。

**状态：** 实验性 - 发布前请审查。

## 基本用法

### CLI

```bash
tsdown --exports
```

### 配置文件

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  exports: true,
})
```

## 生成的内容

### 单个入口

**配置：**
```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  exports: true,
})
```

**在 package.json 中生成：**
```json
{
  "main": "./dist/index.cjs",
  "module": "./dist/index.mjs",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "types": "./dist/index.d.ts",
      "import": "./dist/index.mjs",
      "require": "./dist/index.cjs"
    }
  }
}
```

### 多个入口

**配置：**
```ts
export default defineConfig({
  entry: {
    index: 'src/index.ts',
    utils: 'src/utils.ts',
  },
  format: ['esm', 'cjs'],
  dts: true,
  exports: true,
})
```

**在 package.json 中生成：**
```json
{
  "main": "./dist/index.cjs",
  "module": "./dist/index.mjs",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "types": "./dist/index.d.ts",
      "import": "./dist/index.mjs",
      "require": "./dist/index.cjs"
    },
    "./utils": {
      "types": "./dist/utils.d.ts",
      "import": "./dist/utils.mjs",
      "require": "./dist/utils.cjs"
    }
  }
}
```

## 导出所有文件

包括所有输出文件，而不仅仅是入口点：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  exports: {
    all: true,
  },
})
```

**结果：** 所有 `.mjs`、`.cjs` 和 `.d.ts` 文件都将添加到 exports。

## 开发时源链接

### 开发导出

在开发期间链接到源文件：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  exports: {
    devExports: true,
  },
})
```

**生成：**
```json
{
  "exports": {
    ".": "./src/index.ts"  // 指向源文件
  },
  "publishConfig": {
    "exports": {
      ".": {
        "import": "./dist/index.mjs",
        "require": "./dist/index.cjs"
      }
    }
  }
}
```

**注意：** pnpm/yarn 支持，npm 不支持。

### 条件开发导出

为开发导出使用特定条件：

```ts
export default defineConfig({
  exports: {
    devExports: 'development',
  },
})
```

**生成：**
```json
{
  "exports": {
    ".": {
      "development": "./src/index.ts",
      "import": "./dist/index.mjs",
      "require": "./dist/index.cjs"
    }
  }
}
```

**与 TypeScript customConditions 一起使用：**
```json
// tsconfig.json
{
  "compilerOptions": {
    "customConditions": ["development"]
  }
}
```

## 自定义导出

添加自定义导出映射：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  exports: {
    customExports(pkg, context) {
      // 添加自定义导出
      pkg['./foo'] = './dist/foo.js'

      // 添加 package.json 导出
      pkg['./package.json'] = './package.json'

      return pkg
    },
  },
})
```

## 常见模式

### 完整的库设置

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  exports: true,
  clean: true,
})
```

### 带有开发模式的多个导出

```ts
export default defineConfig({
  entry: {
    index: 'src/index.ts',
    client: 'src/client.ts',
    server: 'src/server.ts',
  },
  format: ['esm', 'cjs'],
  dts: true,
  exports: {
    all: false,  // 仅入口点
    devExports: 'development',
  },
})
```

### Monorepo 包

```ts
export default defineConfig({
  workspace: 'packages/*',
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  exports: true,  // 为每个包生成
})
```

## 验证

### 启用 Publint

验证生成的导出：

```bash
tsdown --exports --publint
```

或在配置中：

```ts
export default defineConfig({
  exports: true,
  publint: true,  // 验证导出
})
```

## 提示

1. **发布前审查** - 检查生成的字段
2. **与 publint 一起使用** - 验证 exports 字段
3. **为库启用** - 特别是多个导出
4. **使用 devExports** - 开发期间更好的 DX
5. **测试导出** - 验证导入正常工作

## 故障排除

### 未生成导出

- 确保设置了 `exports: true`
- 检查构建成功完成
- 验证输出文件存在

### 导出路径错误

- 检查 `outDir` 配置
- 验证入口名称符合预期
- 审查 `format` 设置

### 开发导出不工作

- 仅 pnpm/yarn 支持
- 检查包管理器
- 使用 `publishConfig` 进行发布

### 未导出类型

- 启用 `dts: true`
- 确保安装了 TypeScript
- 检查是否生成了 `.d.ts` 文件

## CLI 示例

```bash
# 生成导出
tsdown --exports

# 带有 publint 验证
tsdown --exports --publint

# 导出所有文件
tsdown --exports

# 带有开发导出
tsdown --exports
```

## 相关选项

- [入口点](option-entry.md) - 配置入口点
- [输出格式](option-output-format.md) - 模块格式
- [DTS](option-dts.md) - 类型声明