---
name: animations
description: 点击动画、运动效果和幻灯片过渡
---

# 动画

点击动画、运动效果和幻灯片过渡。

## 点击动画

### v-click 指令

```md
<div v-click>点击时显示</div>
<div v-click>下次点击时显示</div>
```

### v-clicks 组件

为列表项添加动画：

```md
<v-clicks>

- 项目 1
- 项目 2
- 项目 3

</v-clicks>
```

为嵌套列表设置深度：

```md
<v-clicks depth="2">

- 父项 1
  - 子项 1
  - 子项 2
- 父项 2

</v-clicks>
```

### 点击定位

相对定位：
```md
<div v-click>第 1 个（默认）</div>
<div v-click="+1">第 2 个</div>
<div v-click="-1">与前一个相同</div>
```

绝对定位：
```md
<div v-click="3">在第 3 次点击时显示</div>
<div v-click="[2,5]">在第 2-5 次点击时可见</div>
```

### v-after

与前一个元素一起显示：

```md
<div v-click>主元素</div>
<div v-after>与主元素一起显示</div>
```

### v-switch

根据点击进行条件渲染：

```md
<v-switch>
  <template #1>第一个状态</template>
  <template #2>第二个状态</template>
  <template #3>第三个状态</template>
</v-switch>
```

## 自定义点击次数

```md
---
clicks: 10
---
```

或从特定次数开始：

```md
---
clicksStart: 5
---
```

## 运动动画

使用 @vueuse/motion：

```md
<div
  v-motion
  :initial="{ x: -100, opacity: 0 }"
  :enter="{ x: 0, opacity: 1 }"
>
  动画内容
</div>
```

基于点击的运动：

```md
<div
  v-motion
  :initial="{ scale: 1 }"
  :click-1="{ scale: 1.5 }"
  :click-2="{ scale: 1 }"
>
  点击时缩放
</div>
```

## 幻灯片过渡

在 headmatter 中（所有幻灯片）：

```md
---
transition: slide-left
---
```

单张幻灯片：

```md
---
transition: fade
---
```

### 内置过渡效果

- `fade` / `fade-out`
- `slide-left` / `slide-right`
- `slide-up` / `slide-down`
- `view-transition`（View Transitions API）

### 方向性过渡

前进/后退使用不同的过渡效果：

```md
---
transition: slide-left | slide-right
---
```

### 自定义过渡

定义 CSS 类：

```css
.my-transition-enter-active,
.my-transition-leave-active {
  transition: all 0.5s ease;
}
.my-transition-enter-from,
.my-transition-leave-to {
  opacity: 0;
  transform: translateX(100px);
}
```

使用：`transition: my-transition`

## CSS 类

动画目标获得这些类：
- `.slidev-vclick-target` - 动画元素
- `.slidev-vclick-hidden` - 隐藏状态
- `.slidev-vclick-current` - 当前点击目标
- `.slidev-vclick-prior` - 之前显示的

## 默认动画 CSS

```css
.slidev-vclick-target {
  transition: opacity 100ms ease;
}
.slidev-vclick-hidden {
  opacity: 0;
  pointer-events: none;
}
```
