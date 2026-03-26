---
name: monaco
description: 将代码块转换为功能完整的编辑器
---

# Monaco 编辑器

将代码块转换为功能完整的编辑器。

## 基本用法

````md
```ts {monaco}
console.log('HelloWorld')
```
````

## 差异编辑器

比较两个代码版本：

````md
```ts {monaco-diff}
console.log('原始文本')
~~~
console.log('修改后的文本')
```
````

## 编辑器高度

输入时自动增长：

````md
```ts {monaco} {height:'auto'}
console.log('Hello, World!')
```
````

固定高度：

````md
```ts {monaco} {height:'300px'}
// 代码在这里
```
````

## 配置

参见 `/custom/config-monaco` 了解 Monaco 编辑器自定义选项。
