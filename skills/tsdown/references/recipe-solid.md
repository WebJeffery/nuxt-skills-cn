# Solid 支持

使用 `tsdown` 和 `rolldown-plugin-solid` 或 `unplugin-solid` 构建 Solid 组件库。

## 快速开始

```bash
npx create-tsdown@latest -t solid
```

## 配置

```ts
import solid from 'rolldown-plugin-solid' // 或 'unplugin-solid/rolldown'
import { defineConfig } from 'tsdown'

export default defineConfig({
  entry: ['./src/index.ts'],
  platform: 'neutral',
  dts: true,
  plugins: [solid()],
})
```

## 依赖

安装以下之一：

```bash
# 选项 1：rolldown-plugin-solid
npm install -D rolldown-plugin-solid

# 选项 2：unplugin-solid
npm install -D unplugin-solid
```

## 关键点

- 使用 `platform: 'neutral'` 获得与框架无关的输出
- `dts: true` 生成 TypeScript 声明
- Solid 插件处理 Solid 响应式系统的 JSX 编译

## 相关内容

- [插件](advanced-plugins.md) - 插件配置
- [平台](option-platform.md) - 平台选项