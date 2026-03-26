# 介绍

**tsdown** 是_优雅的库打包器_——一个由 Rolldown（基于 Rust）驱动的快速、简单的 TypeScript 和 JavaScript 库打包器。

## 为什么选择 tsdown？

基于 [Rolldown](https://rolldown.rs) 构建，tsdown 为库作者提供了完整的开箱即用解决方案：

- **简化的配置**：库开发的合理默认值，最少的样板代码
- **库特定功能**：自动 TypeScript 声明、多种输出格式、包验证
- **面向未来**：官方 Rolldown 项目，Rolldown Vite 库模式的基础

## 插件生态系统

支持完整的 Rolldown 插件生态系统以及大多数 Rollup 插件。请参阅[插件](advanced-plugins.md)。

## 它可以打包什么？

- **TypeScript/JavaScript**：带有现代语法的 `.ts`、`.js`
- **TypeScript 声明**：自动生成 `.d.ts` 文件
- **多种格式**：`esm`、`cjs`、`iife`、`umd`
- **资源**：`.json`、`.wasm`、CSS 文件
- 内置 tree shaking、压缩和源映射

## 与 Rolldown 的主要区别

tsdown 用库特定功能包装 Rolldown：
- 从 `package.json` 自动外部化依赖项
- DTS 生成
- `package.json` exports 字段生成
- 带有键盘快捷键的监视模式
- CSS 预处理管道
- 可执行文件打包（SEA）

## 先前作品

灵感来自：Rollup、esbuild、tsup、unbuild。由 Rolldown 驱动。

## 相关内容

- [入门指南](guide-getting-started.md) - 安装和首次构建
- [从 tsup 迁移](guide-migrate-from-tsup.md) - 迁移指南
