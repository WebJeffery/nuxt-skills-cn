# 源映射

生成源映射以调试打包代码。

## 概述

源映射将压缩/打包的代码映射回原始源文件，通过显示原始行号和变量名使调试变得更容易。

## 基本用法

### CLI

```bash
tsdown --sourcemap

# 或内联
tsdown --sourcemap inline
```

### 配置文件

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  sourcemap: true,
})
```

## 源映射类型

### 外部（默认）

生成单独的 `.map` 文件：

```ts
export default defineConfig({
  sourcemap: true,  // 或 'external'
})
```

**输出：**
- `dist/index.mjs`
- `dist/index.mjs.map`

**优点：**
- 较小的包大小
- 可以从生产中排除
- 更快的解析

### 内联

将源映射嵌入包中：

```ts
export default defineConfig({
  sourcemap: 'inline',
})
```

**输出：**
- `dist/index.mjs`（包括作为数据 URL 的源映射）

**优点：**
- 单文件部署
- 保证可用

**缺点：**
- 较大的包大小
- 在生产中暴露

### 隐藏

生成没有引用注释的映射文件：

```ts
export default defineConfig({
  sourcemap: 'hidden',
})
```

**输出：**
- `dist/index.mjs`（无 `//# sourceMappingURL` 注释）
- `dist/index.mjs.map`

**在以下情况下使用：**
- 您希望将映射用于错误报告工具
- 但不想将它们暴露给用户

## 自动启用场景

### 声明映射

如果在 `tsconfig.json` 中启用了 `declarationMap`，则源映射会自动启用：

```json
// tsconfig.json
{
  "compilerOptions": {
    "declarationMap": true
  }
}
```

这还会为 TypeScript 声明生成 `.d.ts.map` 文件。

## 常见模式

### 开发构建

```ts
export default defineConfig((options) => ({
  entry: ['src/index.ts'],
  sourcemap: options.watch,  // 仅在开发中
  minify: !options.watch,
}))
```

### 带外部映射的生产

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  sourcemap: true,  // 外部映射
  minify: true,
})
```

将映射部署到单独的错误报告服务。

### 始终内联（开发工具）

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm'],
  sourcemap: 'inline',
})
```

### 每种格式源映射

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: {
    esm: {
      sourcemap: true,
    },
    iife: {
      sourcemap: 'inline',  // 浏览器内联
    },
  },
})
```

### 带声明映射的 TypeScript 库

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  sourcemap: true,
  dts: {
    sourcemap: true,  // 启用声明映射
  },
})
```

**输出：**
- `dist/index.mjs` + `dist/index.mjs.map`
- `dist/index.cjs` + `dist/index.cjs.map`
- `dist/index.d.ts` + `dist/index.d.ts.map`

## 好处

### 用于开发

- **更快的调试** - 在调试器中看到原始代码
- **更好的错误消息** - 堆栈跟踪显示原始行
- **更容易的断点** - 在源代码上设置断点

### 用于生产

- **错误报告** - 将准确的位置发送到服务
- **监控** - 将错误跟踪到源
- **支持** - 帮助用户准确报告问题

## 性能影响

| 类型 | 包大小 | 解析速度 | 调试 |
|------|-------------|-------------|-----------|
| 无 | 最小 | 最快 | 困难 |
| 外部 | 小 | 快 | 容易 |
| 内联 | 最大 | 较慢 | 容易 |
| 隐藏 | 小 | 快 | 仅工具 |

## CLI 示例

```bash
# 启用源映射
tsdown --sourcemap

# 内联源映射
tsdown --sourcemap inline

# 隐藏源映射
tsdown --sourcemap hidden

# 带源映射的开发
tsdown --watch --sourcemap

# 带外部映射的生产
tsdown --minify --sourcemap

# 无源映射
tsdown --no-sourcemap
```

## 用例

### 本地开发

```ts
export default defineConfig({
  sourcemap: true,
  minify: false,
})
```

### 生产构建

```ts
export default defineConfig({
  sourcemap: 'external',  // 上传到错误服务
  minify: true,
})
```

### 浏览器库

```ts
export default defineConfig({
  format: ['iife'],
  platform: 'browser',
  sourcemap: 'inline',  // 自包含
  globalName: 'MyLib',
})
```

### Node.js CLI 工具

```ts
export default defineConfig({
  format: ['esm'],
  platform: 'node',
  sourcemap: true,
  shims: true,
})
```

## 故障排除

### 源映射不工作

1. **检查输出** - 验证是否生成了 `.map` 文件
2. **检查引用** - 查找 `//# sourceMappingURL=` 注释
3. **检查路径** - 确保相对路径正确
4. **检查工具** - 验证调试器/浏览器支持源映射

### 包大小大

使用外部源映射而不是内联：

```ts
export default defineConfig({
  sourcemap: true,  // 不是 'inline'
})
```

### 未找到源

- 确保源文件相对于映射可访问
- 检查生成的映射中的 `sourceRoot`
- 验证 `sources` 数组中的路径

## 提示

1. **使用外部映射**用于生产（较小的包）
2. **使用内联映射**用于单文件工具
3. **在开发中启用**以获得更好的 DX
4. **上传到错误服务**用于生产调试
5. **使用隐藏映射**当您仅希望它们用于工具时
6. **为 TypeScript 库启用声明映射**

## 相关选项

- [压缩](option-minification.md) - 代码压缩
- [DTS](option-dts.md) - TypeScript 声明
- [监视模式](option-watch-mode.md) - 开发工作流
- [目标](option-target.md) - 语法转换
