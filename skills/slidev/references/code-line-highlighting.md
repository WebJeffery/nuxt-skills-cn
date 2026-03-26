---
name: line-highlighting
description: 使用静态或基于点击的动态高亮来高亮代码块中的特定行
---

# 行高亮

高亮代码块中的特定行。

## 静态高亮

````md
```ts {2,3}
function add(
  a: Ref<number> | number,
  b: Ref<number> | number
) {
  return computed(() => unref(a) + unref(b))
}
```
````

## 动态高亮（基于点击）

使用 `|` 分隔阶段：

````md
```ts {2-3|5|all}
function add(
  a: Ref<number> | number,
  b: Ref<number> | number
) {
  return computed(() => unref(a) + unref(b))
}
```
````

点击进度：第 2-3 行 → 第 5 行 → 所有行

## 特殊值

- `hide` - 隐藏代码块
- `none` - 显示代码但不高亮
- `all` - 高亮所有行

````md
```ts {hide|none|all}
// 隐藏 → 无高亮 → 全部高亮
```
````
````
