---
name: rough-marker
description: 使用 Rough Notation 进行手绘风格高亮
---

# Rough 标记

使用 Rough Notation 进行手绘风格高亮。

## v-mark 指令

```html
<span v-mark>重要文本</span>
```

## 标记类型

```html
<span v-mark.underline>下划线</span>
<span v-mark.circle>圆圈</span>
<span v-mark.highlight>高亮</span>
<span v-mark.strike-through>删除线</span>
<span v-mark.box>方框</span>
```

## 颜色

```html
<span v-mark.red>红色标记</span>
<span v-mark.blue>蓝色标记</span>
```

自定义颜色：
```html
<span v-mark="{ color: '#234' }">自定义颜色</span>
```

## 点击时机

像 v-click 一样工作：

```html
<span v-mark="5">在第 5 次点击时出现</span>
<span v-mark="'+1'">下一次点击</span>
```

## 完整选项

```html
<span v-mark="{ at: 5, color: '#234', type: 'circle' }">
  自定义标记
</span>
```
