---
name: magic-move
description: 使用平滑过渡动画化代码更改
---

# Shiki Magic Move

使用平滑过渡动画化代码更改（类似 Keynote 的 Magic Move）。

## 基本用法

`````md
````md magic-move
```js
console.log(`Step ${1}`)
```
```js
console.log(`Step ${1 + 1}`)
```
```ts
console.log(`Step ${3}` as string)
```
````
````

注意：包装器使用 4 个反引号。

## 带行高亮

`````md
````md magic-move {at:4, lines: true}
```js {*|1|2-5}
let count = 1
function add() {
  count++
}
```

中间的非代码块将被忽略。

```js {*}{lines: false}
let count = 1
const add = () => count += 1
```
````
````

## 工作原理

- 将多个代码块包装为一个
- 每个块是一个"步骤"
- 点击时在步骤之间变形
- 动画期间保留语法高亮

## 资源

- 演练场：https://shiki-magic-move.netlify.app/
