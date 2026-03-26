# 入口点

配置要打包为入口点的文件。

## 概述

入口点是打包过程的起始文件。每个入口点生成一个单独的包。

## 使用模式

### CLI

```bash
# 单个入口
tsdown src/index.ts

# 多个入口
tsdown src/index.ts src/cli.ts

# Glob 模式
tsdown 'src/*.ts'
```

### 配置文件

#### 单个入口

```ts
export default defineConfig({
  entry: 'src/index.ts',
})
```

#### 多个入口（数组）

```ts
export default defineConfig({
  entry: ['src/entry1.ts', 'src/entry2.ts'],
})
```

#### 命名入口（对象）

```ts
export default defineConfig({
  entry: {
    main: 'src/index.ts',
    utils: 'src/utils.ts',
    cli: 'src/cli.ts',
  },
})
```

输出文件将与键匹配：
- `dist/main.mjs`
- `dist/utils.mjs`
- `dist/cli.mjs`

## Glob 模式

使用 glob 模式动态匹配多个文件：

### 所有 TypeScript 文件

```ts
export default defineConfig({
  entry: 'src/**/*.ts',
})
```

### 排除测试文件

```ts
export default defineConfig({
  entry: ['src/*.ts', '!src/*.test.ts'],
})
```

### 使用 Glob 模式的对象入口

在键和值中使用 glob 通配符（`*`）。键中的 `*` 充当占位符，被匹配的文件名（不带扩展名）替换：

```ts
export default defineConfig({
  entry: {
    // 映射 src/foo.ts → dist/lib/foo.js, src/bar.ts → dist/lib/bar.js
    'lib/*': 'src/*.ts',
  },
})
```

#### 对象入口中的否定模式

值可以是带有否定模式（`!`）的数组：

```ts
export default defineConfig({
  entry: {
    'hooks/*': ['src/hooks/*.ts', '!src/hooks/index.ts'],
  },
})
```

多个肯定和否定模式：

```ts
export default defineConfig({
  entry: {
    'utils/*': [
      'src/utils/*.ts',
      'src/utils/*.tsx',
      '!src/utils/index.ts',
      '!src/utils/internal.ts',
    ],
  },
})
```

**警告：** 数组值中的多个肯定模式必须共享相同的基本目录。

### 混合入口

在数组中混合字符串、glob 模式和对象入口：

```ts
export default defineConfig({
  entry: [
    'src/*',
    '!src/foo.ts',
    { main: 'index.ts' },
    { 'lib/*': ['src/*.ts', '!src/bar.ts'] },
  ],
})
```

当输出名称冲突时，对象入口优先。

### Windows 兼容性

在 Windows 上使用正斜杠 `/` 而不是反斜杠 `\`：

```ts
// ✅ 正确
entry: 'src/utils/*.ts'

// ❌ 在 Windows 上错误
entry: 'src\\utils\\*.ts'
```

## 常见模式

### 带有主导出的库

```ts
export default defineConfig({
  entry: 'src/index.ts',
  format: ['esm', 'cjs'],
  dts: true,
})
```

### 带有多个导出的库

```ts
export default defineConfig({
  entry: {
    index: 'src/index.ts',
    client: 'src/client.ts',
    server: 'src/server.ts',
  },
  format: ['esm', 'cjs'],
  dts: true,
})
```

### CLI 工具

```ts
export default defineConfig({
  entry: {
    cli: 'src/cli.ts',
  },
  format: ['esm'],
  platform: 'node',
})
```

### 保留目录结构

与 `unbundle: true` 一起使用以保持文件结构：

```ts
export default defineConfig({
  entry: ['src/**/*.ts', '!**/*.test.ts'],
  unbundle: true,
  format: ['esm'],
  dts: true,
})
```

这将输出与源结构匹配的文件：
- `src/index.ts` → `dist/index.mjs`
- `src/utils/helper.ts` → `dist/utils/helper.mjs`

## 提示

1. **使用 glob 模式**处理多个相关文件
2. **使用对象语法**自定义输出名称
3. **使用否定模式**排除测试文件 `!**/*.test.ts`
4. **与 unbundle 结合**保留目录结构
5. **使用命名入口**更好地控制输出文件名
