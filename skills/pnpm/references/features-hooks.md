---
name: pnpm-hooks
description: 使用 pnpmfile 钩子自定义包解析和依赖行为
---

# pnpm 钩子

pnpm 通过 `.pnpmfile.cjs` 提供钩子来自定义包的解析方式和元数据的处理方式。

## 设置

在工作区根目录创建 `.pnpmfile.cjs`：

```js
// .pnpmfile.cjs
function readPackage(pkg, context) {
  // 修改包元数据
  return pkg
}

function afterAllResolved(lockfile, context) {
  // 修改锁文件
  return lockfile
}

module.exports = {
  hooks: {
    readPackage,
    afterAllResolved
  }
}
```

## readPackage 钩子

在解析之前为每个包调用。用于修改依赖项、添加缺失的同伴依赖或修复损坏的包。

### 添加缺失的同伴依赖

```js
function readPackage(pkg, context) {
  if (pkg.name === 'some-broken-package') {
    pkg.peerDependencies = {
      ...pkg.peerDependencies,
      react: '*'
    }
    context.log(`Added react peer dep to ${pkg.name}`)
  }
  return pkg
}
```

### 覆盖依赖版本

```js
function readPackage(pkg, context) {
  // 修复所有 lodash 版本
  if (pkg.dependencies?.lodash) {
    pkg.dependencies.lodash = '^4.17.21'
  }
  if (pkg.devDependencies?.lodash) {
    pkg.devDependencies.lodash = '^4.17.21'
  }
  return pkg
}
```

### 删除不需要的依赖

```js
function readPackage(pkg, context) {
  // 删除导致问题的可选依赖
  if (pkg.optionalDependencies?.fsevents) {
    delete pkg.optionalDependencies.fsevents
  }
  return pkg
}
```

### 替换包

```js
function readPackage(pkg, context) {
  // 替换已弃用的包
  if (pkg.dependencies?.['old-package']) {
    pkg.dependencies['new-package'] = pkg.dependencies['old-package']
    delete pkg.dependencies['old-package']
  }
  return pkg
}
```

### 修复损坏的包

```js
function readPackage(pkg, context) {
  // 修复不正确的 exports 字段
  if (pkg.name === 'broken-esm-package') {
    pkg.exports = {
      '.': {
        import: './dist/index.mjs',
        require: './dist/index.cjs'
      }
    }
  }
  return pkg
}
```

## afterAllResolved 钩子

在生成锁文件后调用。用于解析后修改。

```js
function afterAllResolved(lockfile, context) {
  // 记录所有已解析的包
  context.log(`Resolved ${Object.keys(lockfile.packages || {}).length} packages`)
  
  // 如果需要，修改锁文件
  return lockfile
}
```

## 上下文对象

`context` 对象提供实用程序：

```js
function readPackage(pkg, context) {
  // 记录消息
  context.log('Processing package...')
  
  return pkg
}
```

## 与 TypeScript 一起使用

对于类型提示，使用 JSDoc：

```js
// .pnpmfile.cjs

/**
 * @param {import('type-fest').PackageJson} pkg
 * @param {{ log: (msg: string) => void }} context
 * @returns {import('type-fest').PackageJson}
 */
function readPackage(pkg, context) {
  return pkg
}

module.exports = {
  hooks: {
    readPackage
  }
}
```

## 常见模式

### 按包名条件

```js
function readPackage(pkg, context) {
  switch (pkg.name) {
    case 'package-a':
      pkg.dependencies.foo = '^2.0.0'
      break
    case 'package-b':
      delete pkg.optionalDependencies.bar
      break
  }
  return pkg
}
```

### 应用于所有包

```js
function readPackage(pkg, context) {
  // 删除所有可选的 fsevents
  if (pkg.optionalDependencies) {
    delete pkg.optionalDependencies.fsevents
  }
  return pkg
}
```

### 调试解析

```js
function readPackage(pkg, context) {
  if (process.env.DEBUG_PNPM) {
    context.log(`${pkg.name}@${pkg.version}`)
    context.log(`  deps: ${Object.keys(pkg.dependencies || {}).join(', ')}`)
  }
  return pkg
}
```

## 钩子与覆盖

| 功能 | 钩子 (.pnpmfile.cjs) | 覆盖 |
|---------|----------------------|-----------|
| 复杂性 | 可以使用 JavaScript 逻辑 | 仅声明式 |
| 范围 | 任何包元数据 | 仅版本 |
| 用例 | 复杂修复、条件逻辑 | 简单版本固定 |

**优先使用覆盖** 进行简单的版本修复。**使用钩子** 当您需要：
- 条件逻辑
- 非版本修改（exports、同伴依赖）
- 日志/调试

## 故障排除

### 钩子未运行

1. 确保文件名为 `.pnpmfile.cjs`（不是 `.js`）
2. 检查文件位于工作区根目录
3. 运行 `pnpm install` 以触发钩子

### 调试钩子

```bash
# 查看钩子日志
pnpm install --reporter=append-only
```

<!--
源引用:
- https://pnpm.io/pnpmfile
-->
