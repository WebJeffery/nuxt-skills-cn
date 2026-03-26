# tsdown Claude Code 技能

帮助 Claude Code 理解和使用 [tsdown](https://tsdown.dev)（优雅的库打包器）的代理技能。

## 安装

```bash
npx skills add rolldown/tsdown
```

这会将 tsdown 技能添加到您的 Claude Code 配置中。

## 包含内容

tsdown 技能为 Claude Code 提供以下知识：

- **核心概念** - tsdown 是什么、为什么要使用它、关键功能
- **配置** - 配置文件格式、选项、多个配置、工作区支持
- **构建选项** - 入口点、输出格式、类型声明、目标
- **依赖处理** - 外部/内联依赖、自动外部化
- **输出增强** - Shims、CJS 默认值、包导出
- **框架支持** - React、Vue、Solid、Svelte 集成
- **高级功能** - 插件、钩子、编程式 API、Rolldown 选项
- **CLI 命令** - 所有 CLI 选项和使用模式
- **迁移** - 从 tsup 迁移到 tsdown

## 使用方法

安装后，Claude Code 将在以下情况下自动使用 tsdown 知识：

- 构建 TypeScript/JavaScript 库
- 为库项目配置打包器
- 设置类型声明生成
- 处理多格式构建（ESM、CJS、IIFE、UMD）
- 从 tsup 迁移
- 构建框架组件库

### 示例提示

```
设置 tsdown 以使用 ESM 和 CJS 格式构建我的 TypeScript 库
```

```
配置 tsdown 以生成类型声明并为浏览器打包
```

```
将 React 支持添加到我的 tsdown 配置中，使用 Fast Refresh
```

```
帮助我从 tsup 迁移到 tsdown
```

```
使用 tsdown 工作区支持设置 monorepo 构建
```

## 文档

- [tsdown 文档](https://tsdown.dev)
- [GitHub 仓库](https://github.com/rolldown/tsdown)
- [Rolldown](https://rolldown.rs)
- [迁移指南](https://tsdown.dev/guide/migrate-from-tsup)

## 许可证

MIT
