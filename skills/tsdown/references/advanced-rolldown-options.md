# 自定义 Rolldown 选项

将选项直接传递给底层的 Rolldown 打包器。

## 概述

tsdown 使用 [Rolldown](https://rolldown.rs) 作为其核心打包引擎。您可以直接覆盖 Rolldown 的输入和输出选项以进行细粒度控制。

**警告：** 在覆盖选项之前，您应该熟悉 Rolldown 的行为。请参阅 [Rolldown 配置选项](https://rolldown.rs/options/input) 文档。

## 输入选项

### 使用对象

```ts
export default defineConfig({
  inputOptions: {
    cwd: './custom-directory',
  },
})
```

### 使用函数

根据输出格式动态修改选项：

```ts
export default defineConfig({
  inputOptions(inputOptions, format) {
    inputOptions.cwd = './custom-directory'
    return inputOptions
  },
})
```

## 输出选项

### 使用对象

```ts
export default defineConfig({
  outputOptions: {
    legalComments: 'inline',
  },
})
```

### 使用函数

```ts
export default defineConfig({
  outputOptions(outputOptions, format) {
    if (format === 'esm') {
      outputOptions.legalComments = 'inline'
    }
    return outputOptions
  },
})
```

## 常见用例

### 保留法律注释

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  outputOptions: {
    legalComments: 'inline',
  },
})
```

### 自定义工作目录

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  inputOptions: {
    cwd: './packages/my-lib',
  },
})
```

### 特定格式选项

```ts
export default defineConfig({
  entry: ['src/index.ts'],
  format: ['esm', 'cjs'],
  outputOptions(outputOptions, format) {
    if (format === 'esm') {
      outputOptions.legalComments = 'inline'
    }
    return outputOptions
  },
})
```

## 何时使用

- 当 tsdown 未公开特定的 Rolldown 选项时
- 用于特定格式的 Rolldown 自定义
- 用于高级打包场景

## 提示

1. **在覆盖选项之前阅读 Rolldown 文档**
2. **使用函数**进行特定格式的自定义
3. **覆盖默认值时彻底测试**
4. **优先使用 tsdown 选项**（例如，使用 `minify` 而不是通过 `outputOptions` 设置）

## 相关

- [插件](advanced-plugins.md) - 插件系统
- [Hooks](advanced-hooks.md) - 生命周期钩子
- [配置文件](option-config-file.md) - 配置选项