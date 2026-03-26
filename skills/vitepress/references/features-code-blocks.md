---
name: vitepress-code-blocks
description: 语法高亮、行高亮、彩色差异、焦点和行号
---

# 代码块

VitePress 使用 Shiki 进行语法高亮,具有强大的代码块功能。

## 语法高亮

在开始反引号后指定语言:

````md
```js
export default {
  name: 'MyComponent'
}
```
````

支持 Shiki 中可用的[所有语言](https://shiki.style/languages)。

## 行高亮

高亮特定行:

````md
```js{4}
export default {
  data() {
    return {
      msg: 'Highlighted!'  // Line 4 highlighted
    }
  }
}
```
````

多行和范围:

````md
```js{1,4,6-8}
// Line 1 highlighted
export default {
  data() {
    return {      // Line 4
      msg: 'Hi',
      foo: 'bar', // Lines 6-8
      baz: 'qux'
    }
  }
}
```
````

使用注释的内联高亮:

````md
```js
export default {
  data() {
    return {
      msg: 'Highlighted!' // [!code highlight]
    }
  }
}
```
````

## 焦点

模糊其他代码并聚焦特定行:

````md
```js
export default {
  data() {
    return {
      msg: 'Focused!' // [!code focus]
    }
  }
}
```
````

聚焦多行:

```js
// [!code focus:3]
```

## 彩色差异

显示添加和删除:

````md
```js
export default {
  data() {
    return {
      msg: 'Removed' // [!code --]
      msg: 'Added'   // [!code ++]
    }
  }
}
```
````

## 错误和警告

将行着色为错误或警告:

````md
```js
export default {
  data() {
    return {
      msg: 'Error',   // [!code error]
      msg: 'Warning'  // [!code warning]
    }
  }
}
```
````

## 行号

全局启用:

```ts
// .vitepress/config.ts
export default {
  markdown: {
    lineNumbers: true
  }
}
```

每块覆盖:

````md
```ts:line-numbers
// Line numbers enabled
const a = 1
```

```ts:no-line-numbers
// Line numbers disabled
const b = 2
```
````

从特定数字开始:

````md
```ts:line-numbers=5
// Starts at line 5
const a = 1  // This is line 5
const b = 2  // This is line 6
```
````

## 代码组

选项卡式代码块:

````md
::: code-group

```js [JavaScript]
export default { /* ... */ }
```

```ts [TypeScript]
export default defineConfig({ /* ... */ })
```

:::
````

## 导入代码片段

从外部文件:

```md
<<< @/snippets/snippet.js
```

使用高亮:

```md
<<< @/snippets/snippet.js{2,4-6}
```

特定区域:

```md
<<< @/snippets/snippet.js#regionName{1,2}
```

使用语言和行号:

```md
<<< @/snippets/snippet.cs{1,2,4-6 c#:line-numbers}
```

在代码组中:

```md
::: code-group

<<< @/snippets/config.js [JavaScript]
<<< @/snippets/config.ts [TypeScript]

:::
```

## 文件标签

将文件名标签添加到代码块:

````md
```js [vite.config.js]
export default defineConfig({})
```
````

## 关键点

- 使用 `// [!code highlight]` 进行内联高亮
- 使用 `// [!code focus]` 聚焦并带有模糊效果
- 使用 `// [!code ++]` 和 `// [!code --]` 进行差异
- 使用 `// [!code error]` 和 `// [!code warning]` 进行状态
- `:line-numbers` 和 `:no-line-numbers` 控制每块的行号
- 导入中的 `@` 引用源根目录
- 代码组创建选项卡式界面

<!--
Source references:
- https://vitepress.dev/guide/markdown#syntax-highlighting-in-code-blocks
- https://vitepress.dev/guide/markdown#line-highlighting-in-code-blocks
-->
