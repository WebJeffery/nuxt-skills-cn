---
name: monaco-run
description: 直接在编辑器中运行代码并查看结果
---

# Monaco 运行器

直接在编辑器中运行代码并查看结果。

## 基本用法

````md
```ts {monaco-run}
function distance(x: number, y: number) {
  return Math.sqrt(x ** 2 + y ** 2)
}
console.log(distance(3, 4))
```
````

显示"运行"按钮并在代码下方显示输出。

## 禁用自动运行

````md
```ts {monaco-run} {autorun:false}
console.log('点击播放按钮运行我')
```
````

## 点击时显示输出

````md
```ts {monaco-run} {showOutputAt:'+1'}
console.log('在 1 次点击后显示')
```
````

## 支持的语言

- JavaScript
- TypeScript

对于其他语言，在 `/custom/config-code-runners` 中配置自定义代码运行器。
