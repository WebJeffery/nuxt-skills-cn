---
title: 使用 mode="out-in" 进行顺序元素过渡
impact: MEDIUM
impactDescription: 没有过渡模式，进入和离开元素同时动画，导致重叠和布局问题
type: best-practice
tags: [vue3, transition, animation, mode, out-in, in-out]
---

# 使用 mode="out-in" 进行顺序元素过渡

**影响：中等** - 默认情况下，Vue 的 `<Transition>` 同时运行进入和离开动画。这导致旧元素和新元素在过渡期间重叠，通常导致视觉故障、布局跳跃或元素堆叠。使用 `mode="out-in"` 确保旧元素完全动画离开，然后新元素进入。

## 任务清单

- [ ] 在互斥元素之间转换时使用 `mode="out-in"`
- [ ] 这对于按钮、选项卡或不应该重叠的内容尤其重要
- [ ] 仅对特定的重叠效果考虑 `mode="in-out"`（很少）
- [ ] 没有模式，两个动画并行运行（默认行为）

**有问题的代码：**
```vue
<template>
  <!-- 错误：没有模式 - 按钮在过渡期间重叠！ -->
  <Transition name="fade">
    <button v-if="isEditing" key="save" @click="save">保存</button>
    <button v-else key="edit" @click="edit">编辑</button>
  </Transition>
</template>

<style>
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
</style>

<!--
结果：两个按钮在过渡期间都可见
- 旧按钮淡出
- 新按钮同时淡入
- 两个按钮都占用空间时布局中断
-->
```

**正确的代码：**
```vue
<template>
  <!-- 好：out-in 模式确保顺序动画 -->
  <Transition name="fade" mode="out-in">
    <button v-if="isEditing" key="save" @click="save">保存</button>
    <button v-else key="edit" @click="edit">编辑</button>
  </Transition>
</template>

<style>
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
</style>

<!--
结果：干净的顺序动画
1. 编辑按钮完全淡出
2. 保存按钮淡入
没有重叠，没有布局问题
-->
```

## 过渡模式说明

### 没有模式（默认）
```
时间：     |----- 离开动画 -----|
          |----- 进入动画 -----|

元素同时动画（并行）
```

### mode="out-in"（大多数情况推荐）
```
时间：     |----- 离开动画 -----|
                                      |----- 进入动画 -----|

旧元素先离开，然后新元素进入（顺序）
```

### mode="in-out"（罕见用例）
```
时间：                                 |----- 离开动画 -----|
          |----- 进入动画 -----|

新元素先进入，然后旧元素离开
```

## 何时使用每种模式

### 使用 `mode="out-in"` 用于：
- 切换按钮（编辑/保存、播放/暂停）
- 选项卡内容切换
- 多步骤表单
- 基于状态的内容（加载/错误/成功）
- 任何互斥内容

```vue
<template>
  <!-- 选项卡内容切换 -->
  <Transition name="slide" mode="out-in">
    <component :is="currentTabComponent" :key="currentTab" />
  </Transition>

  <!-- 基于状态的 UI -->
  <Transition name="fade" mode="out-in">
    <LoadingSpinner v-if="loading" key="loading" />
    <ErrorMessage v-else-if="error" key="error" :message="error" />
    <DataDisplay v-else key="data" :data="data" />
  </Transition>
</template>
```

### 使用 `mode="in-out"` 用于：
- 卡片翻转效果，新内容出现在旧内容后面
- 特定的设计要求，其中重叠是有意的
- 实际上很少使用

```vue
<template>
  <!-- 卡片翻转效果 - 新卡片在后面滑入 -->
  <Transition name="flip" mode="in-out">
    <div :key="cardId" class="card">{{ cardContent }}</div>
  </Transition>
</template>
```

### 没有模式用于：
- 需要重叠的交叉淡入淡出效果
- 具有平滑混合的图像画廊
- 背景过渡

```vue
<template>
  <!-- 图像交叉淡入淡出 - 重叠创建平滑混合 -->
  <Transition name="crossfade">
    <img :key="currentImage" :src="currentImage" class="gallery-image" />
  </Transition>
</template>

<style>
.gallery-image {
  position: absolute; /* 将图像堆叠在一起 */
}

.crossfade-enter-active,
.crossfade-leave-active {
  transition: opacity 0.5s;
}

.crossfade-enter-from,
.crossfade-leave-to {
  opacity: 0;
}
</style>
```

## 处理绝对定位

如果您必须对非重叠元素使用默认模式（没有模式），请使用绝对定位：

```vue
<template>
  <div class="container">
    <Transition name="slide">
      <div v-if="showA" key="a" class="panel">面板 A</div>
      <div v-else key="b" class="panel">面板 B</div>
    </Transition>
  </div>
</template>

<style>
.container {
  position: relative;
  height: 200px; /* 需要固定高度 */
}

.panel {
  position: absolute;
  width: 100%;
}

.slide-enter-active,
.slide-leave-active {
  transition: transform 0.3s, opacity 0.3s;
}

.slide-enter-from {
  transform: translateX(100%);
  opacity: 0;
}

.slide-leave-to {
  transform: translateX(-100%);
  opacity: 0;
}
</style>
```

## 参考
- [Vue.js Transition Modes](https://vuejs.org/guide/built-ins/transition.html#transition-modes)
