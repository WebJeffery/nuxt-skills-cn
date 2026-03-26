---
name: unocss-shortcuts
description: 将多个工具规则组合成单个简写类
---

# UnoCSS 快捷方式

快捷方式将多个规则组合成单个简写，灵感来自 Windi CSS。

## 静态快捷方式

定义为将快捷方式名称映射到工具组合的对象：

```ts
shortcuts: {
  // 组合多个工具类
  'btn': 'py-2 px-4 font-semibold rounded-lg shadow-md',
  'btn-green': 'text-white bg-green-500 hover:bg-green-700',
  // 单个工具类别名
  'red': 'text-red-100',
}
```

用法：
```html
<button class="btn btn-green">Click me</button>
```

## 动态快捷方式

使用 RegExp 匹配器和函数，类似于动态规则：

```ts
shortcuts: [
  // 静态快捷方式也可以在数组中
  {
    btn: 'py-2 px-4 font-semibold rounded-lg shadow-md',
  },
  // 动态快捷方式
  [/^btn-(.*)$/, ([, c]) => `bg-${c}-400 text-${c}-100 py-2 px-4 rounded-lg`],
]
```

现在 `btn-green` 和 `btn-red` 生成：

```css
.btn-green {
  padding: 0.5rem 1rem;
  --un-bg-opacity: 1;
  background-color: rgb(74 222 128 / var(--un-bg-opacity));
  border-radius: 0.5rem;
  --un-text-opacity: 1;
  color: rgb(220 252 231 / var(--un-text-opacity));
}
```

## 在快捷方式中访问主题

动态快捷方式接收带有主题访问权限的上下文：

```ts
shortcuts: [
  [/^badge-(.*)$/, ([, c], { theme }) => {
    if (Object.keys(theme.colors).includes(c))
      return `bg-${c}4:10 text-${c}5 rounded`
  }],
]
```

## 快捷方式图层

快捷方式默认输出到 `shortcuts` 图层。使用以下配置：

```ts
shortcutsLayer: 'my-shortcuts-layer'
```

## 关键点

- 后面的快捷方式具有更高的优先级
- 快捷方式可以引用其他快捷方式
- 动态快捷方式的工作方式类似于动态规则
- 快捷方式在构建时展开，而不是运行时
- 所有变体都适用于快捷方式（`hover:btn`、`dark:btn` 等）

<!--
源参考：
- https://unocss.dev/config/shortcuts
-->
