# 输出目录

配置打包文件的输出目录。

## 概述

默认情况下，tsdown 将打包文件输出到 `dist` 目录。您可以使用 `outDir` 选项自定义此位置。

## 基本用法

### CLI

```bash
# 默认输出到 dist/
tsdown

# 自定义输出目录
tsdown --out-dir build
tsdown -d lib
```

### 配置文件

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  outDir: 'build',
})
```

## 常见模式

### 标准库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  outDir: 'dist',  // 默认
  dts: true,
})
```

**输出：**
```
dist/
├── index.mjs
├── index.cjs
└── index.d.ts
```

### 按格式分离目录

```ts
export default defineConfig([
  {
    entry: ['src/index.ts'],
    format: ['esm'],
    outDir: 'dist/esm',
  },
  {
    entry: ['src/index.ts'],
    format: ['cjs'],
    outDir: 'dist/cjs',
  },
])
```

**输出：**
```
dist/
├── esm/
│   └── index.js
└── cjs/
    └── index.js
```

### Monorepo 包

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  outDir: 'lib',  // 自定义目录
  clean: true,
})
```

### 构建到根目录

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  outDir: '.',  // 输出到项目根目录（不推荐）
  clean: false,  // 不要清理根目录！
})
```

**警告：** 输出到根目录时要小心，以避免删除重要文件。

## 输出扩展名

### 自定义扩展名

使用 `outExtensions` 控制文件扩展名：

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  outDir: 'dist',
  outExtensions({ format }) {
    return {
      js: format === 'esm' ? '.mjs' : '.cjs',
    }
  },
})
```

### 默认扩展名

| 格式 | 默认扩展名 | 使用 `type: "module"` |
|--------|-------------------|----------------------|
| `esm` | `.mjs` | `.js` |
| `cjs` | `.cjs` | `.js` |
| `iife` | `.global.js` | `.global.js` |
| `umd` | `.umd.js` | `.umd.js` |

### 使用 .js 扩展名的 ESM

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'],
  outExtensions: () => ({ js: '.js' }),
})
```

需要在 package.json 中使用 `"type": "module"`。

## 文件命名

### 入口名称

根据入口名称控制输出文件名：

```ts
export default defineConfig({
  entry: {
    index: 'src/index.ts',
    utils: 'src/utils.ts',
  },
  outDir: 'dist',
})
```

**输出：**
```
dist/
├── index.mjs
└── utils.mjs
```

### Glob 入口

```ts
export default defineConfig({
  entry: ['src/**/*.ts', '!**/*.test.ts'],
  outDir: 'dist',
  unbundle: true,  // 保留结构
})
```

**输出：**
```
dist/
├── index.mjs
├── utils/
│   └── helper.mjs
└── components/
    └── button.mjs
```

## 多次构建

### 相同输出目录

```ts
export default defineConfig([
  {
    entry: ['src/index.ts'],
    outDir: 'dist',
    clean: true,  // 首先清理
  },
  {
    entry: ['src/cli.ts'],
    outDir: 'dist',
    clean: false,  // 不要再次清理
  },
])
```

### 不同输出目录

```ts
export default defineConfig([
  {
    entry: ['src/index.ts'],
    format: ['esm', 'cjs'],
    outDir: 'dist/lib',
  },
  {
    entry: ['src/cli.ts'],
    format: ['esm'],
    outDir: 'dist/bin',
  },
])
```

## CLI 示例

```bash
# 默认
tsdown

# 自定义目录
tsdown --out-dir build
tsdown -d lib

# 嵌套目录
tsdown --out-dir dist/lib

# 与其他选项一起
tsdown --out-dir build --format esm,cjs --dts
```

## 提示

1. **使用默认 `dist`**用于标准项目
2. **小心使用根目录** - 避免使用 `outDir: '.'`
3. **构建前清理** - 使用 `clean: true`
4. **一致的命名** - 匹配您的项目约定
5. **按格式分离**（如果需要清晰度）
6. **检查 .gitignore** - 确保输出目录被忽略

## 故障排除

### 文件不在预期位置

- 检查 `outDir` 配置
- 验证构建成功完成
- 查找路径中的拼写错误

### 文件意外被删除

- 检查是否 `clean: true`
- 确保 outDir 不与源代码重叠
- 不要使用根目录作为 outDir

### 权限错误

- 检查写入权限
- 确保目录未被锁定
- 尝试不同的位置

## 相关选项

- [清理](option-cleaning.md) - 清理输出目录
- [入口点](option-entry.md) - 入口点
- [输出格式](option-output-format.md) - 模块格式
- [Unbundle](option-unbundle.md) - 保留结构
