---
title: TransitionGroup 子元素必须有唯一的键
impact: HIGH
impactDescription: 缺少或非唯一的键会导致动画损坏和 Vue 警告
type: gotcha
tags: [vue3, transition-group, animation, key, v-for, list-animation]
---

# TransitionGroup 子元素必须有唯一的键

**影响: HIGH** - 与常规的 `<Transition>` 不同，`<TransitionGroup>` 组件**总是要求**每个子元素上都有唯一的 `key` 属性。没有键，Vue 无法跟踪单个项目，动画将损坏，你会看到控制台警告。

这是与 `<Transition>` 的根本区别，后者作用于单个元素。由于 `<TransitionGroup>` 为元素列表设置动画，Vue 需要键来跟踪哪些项目正在进入、离开或移动。

## 任务清单

- [ ] 确保 `<TransitionGroup>` 的每个直接子元素都有唯一的 `key` 属性
- [ ] 使用稳定的标识符（ID）而不是数组索引作为键
- [ ] 不要在不同时间为不同项目使用相同的键
- [ ] 使用 `v-for` 时，始终包含 `:key` 绑定

**不正确 - 缺少键:**
```vue
<template>
  <!-- 错误: 缺少键导致动画损坏 -->
  <TransitionGroup name="list" tag="ul">
    <li v-for="item in items">
      {{ item.name }}
    </li>
  </TransitionGroup>
</template>
```

**不正确 - 使用索引作为键:**
```vue
<template>
  <!-- 有问题: 当列表更改时，索引键导致错误的动画 -->
  <TransitionGroup name="list" tag="ul">
    <li v-for="(item, index) in items" :key="index">
      {{ item.name }}
    </li>
  </TransitionGroup>
</template>
```

使用索引作为键时:
- 如果删除索引 2 处的项目，原本是索引 3 的项目变为索引 2
- Vue 认为索引 2 更改了内容而不是被删除
- 动画在错误的元素上触发

**正确 - 唯一稳定的键:**
```vue
<template>
  <!-- 正确: 唯一的 ID 键用于正确跟踪 -->
  <TransitionGroup name="list" tag="ul">
    <li v-for="item in items" :key="item.id">
      {{ item.name }}
    </li>
  </TransitionGroup>
</template>
```

**正确 - 静态子元素上的键:**
```vue
<template>
  <!-- 正确: 即使是静态子元素在 TransitionGroup 中也需要键 -->
  <TransitionGroup name="fade" tag="div">
    <span v-if="showA" key="a">内容 A</span>
    <span v-if="showB" key="b">内容 B</span>
    <span v-if="showC" key="c">内容 C</span>
  </TransitionGroup>
</template>
```

## 常见错误消息

没有键时，你会看到如下警告:
```
[Vue warn]: <TransitionGroup> children must be keyed.
```

## 使用可拖动库

将 `<TransitionGroup>` 与 `vue.draggable.next` 等库一起使用时，键问题可能导致错误:

```javascript
// 没有正确键可能看到的错误
TypeError: domElement is null
```

确保所有可拖动项目都有唯一的键:

```vue
<template>
  <draggable v-model="items" item-key="id">
    <template #item="{ element }">
      <TransitionGroup name="list" tag="div">
        <div :key="element.id">{{ element.name }}</div>
      </TransitionGroup>
    </template>
  </draggable>
</template>
```

## 参考
- [Vue.js TransitionGroup](https://vuejs.org/guide/built-ins/transition-group.html)
- [Vue.js 列表渲染键](https://vuejs.org/guide/essentials/list.html#maintaining-state-with-key)
