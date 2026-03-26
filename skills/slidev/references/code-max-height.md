---
name: max-height
description: 为代码块设置固定高度，长代码可滚动
---

# 代码块最大高度

为代码块设置固定高度，长代码可滚动。

## 用法

````md
```ts {2|3|7|12}{maxHeight:'100px'}
function add(
  a: Ref<number> | number,
  b: Ref<number> | number
) {
  return computed(() => unref(a) + unref(b))
}
/// ...根据需要添加任意多行
const c = add(1, 2)
```
````

## 与行高亮占位符结合使用

当您只需要 maxHeight 时，使用 `{*}`：

````md
```ts {*}{maxHeight:'100px'}
// 此处为长代码
```
````

## 使用场景

当代码太长无法放在一张幻灯片上，但您希望通过滚动显示全部代码时。
