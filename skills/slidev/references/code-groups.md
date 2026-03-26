---
name: code-groups
description: 使用选项卡和自动图标对多个代码块进行分组
---

# 代码组

使用选项卡和自动图标对多个代码块进行分组。

## 要求

在 headmatter 中启用 Comark 语法：

```md
---
comark: true
---
```

## 语法

````md
::code-group

```sh [npm]
npm i @slidev/cli
```

```sh [yarn]
yarn add @slidev/cli
```

```sh [pnpm]
pnpm add @slidev/cli
```

::
````

## 标题图标匹配

图标按标题名称自动匹配。安装 `@iconify-json/vscode-icons` 以获取内置图标。

支持：npm、yarn、pnpm、bun、deno、vue、react、typescript、javascript 等。

## 自定义图标

在标题中使用 `~icon~` 语法：

````md
```js [npm ~i-uil:github~]
console.log('Hello!')
```
````

要求：
1. 安装图标集：`pnpm add @iconify-json/uil`
2. 添加到 `uno.config.ts` 中的 safelist：

```ts
export default defineConfig({
  safelist: ['i-uil:github']
})
```
