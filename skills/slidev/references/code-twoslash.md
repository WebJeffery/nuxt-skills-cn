---
name: twoslash
description: 在代码块中内联或悬停时显示 TypeScript 类型信息
---

# TwoSlash 集成

在代码块中内联或悬停时显示 TypeScript 类型信息。

## 用法

````md
```ts twoslash
import { ref } from 'vue'

const count = ref(0)
//            ^?
```
````

## 功能

- 悬停时显示类型信息
- 使用 `^?` 进行内联类型注释
- 显示错误和警告
- 完整的 TypeScript 编译器集成

## 注释

```ts twoslash
const count = ref(0)
//            ^?
// 显示：const count: Ref<number>
```

## 使用场景

非常适合 TypeScript/JavaScript 教学材料，其中显示类型有助于理解。

## 资源

- TwoSlash 文档：https://twoslash.netlify.app/
