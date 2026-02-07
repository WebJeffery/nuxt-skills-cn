---
title: 使用 .once 修饰符进行单次使用的事件处理
impact: LOW
impactDescription: .once 修饰符在首次触发后自动移除事件监听器，防止重复调用处理程序
type: best-practice
tags: [vue3, events, modifiers, once, event-handling]
---

# 使用 .once 修饰符进行单次使用的事件处理

**影响: 低** - Vue 为事件监听器提供了 `.once` 修饰符，该修饰符在事件触发一次后自动移除监听器。这对于一次性事件（如初始化回调、首次交互跟踪或一次性动画）非常有用。

## 任务清单

- [ ] 对应该只触发一次的事件使用 `.once`
- [ ] 考虑对分析首次交互跟踪使用 `.once`
- [ ] 对初始化事件使用 `.once`
- [ ] 记住 `.once` 对原生和组件事件都有效

## 基本用法

**组件事件:**
```vue
<template>
  <!-- 处理程序仅在第一次触发时运行，然后停止监听 -->
  <ChildComponent @initialized.once="handleInit" />

  <!-- 首次用户交互跟踪 -->
  <UserForm @submit.once="trackFirstSubmit" />
</template>

<script setup>
function handleInit(data) {
  console.log('组件已初始化:', data)
  // 这只运行一次，即使子组件多次发出 'initialized' 事件
}

function trackFirstSubmit() {
  analytics.track('first_form_submit')
}
</script>
```

**原生 DOM 事件:**
```vue
<template>
  <!-- 仅第一次点击 -->
  <button @click.once="showWelcomeMessage">
    点击查看欢迎消息（一次）
  </button>

  <!-- 仅第一次滚动 -->
  <div @scroll.once="loadMoreContent">
    滚动以加载更多
  </div>
</template>
```

## 常见用例

### 一次性初始化
```vue
<template>
  <ThirdPartyChart
    @ready.once="onChartReady"
    :data="chartData"
  />
</template>

<script setup>
function onChartReady(chartInstance) {
  // 存储引用，配置图表
  // 只需要做一次
  chartRef.value = chartInstance
  chartInstance.setOption(customOptions)
}
</script>
```

### 首次交互分析
```vue
<template>
  <article @click.once="trackEngagement">
    <h2>{{ article.title }}</h2>
    <p>{{ article.excerpt }}</p>
  </article>
</template>

<script setup>
function trackEngagement() {
  analytics.track('article_first_click', {
    articleId: article.id
  })
}
</script>
```

### 懒加载触发器
```vue
<template>
  <div
    ref="lazyContainer"
    @mouseenter.once="loadHeavyContent"
  >
    <template v-if="loaded">
      <HeavyComponent :data="data" />
    </template>
    <template v-else>
      <Placeholder />
    </template>
  </div>
</template>

<script setup>
const loaded = ref(false)
const data = ref(null)

async function loadHeavyContent() {
  data.value = await fetchData()
  loaded.value = true
}
</script>
```

### 一次性动画
```vue
<template>
  <Transition
    @after-enter.once="onFirstAppearance"
  >
    <div v-if="show" class="animated-content">
      内容
    </div>
  </Transition>
</template>

<script setup>
function onFirstAppearance() {
  // 跟踪用户第一次看到此元素
  // 如果元素离开并重新进入，不会再次触发
}
</script>
```

## 与其他修饰符结合使用

```vue
<template>
  <!-- Once + 阻止默认 -->
  <form @submit.once.prevent="handleFirstSubmit">
    ...
  </form>

  <!-- Once + 停止传播 -->
  <div @click.once.stop="handleClick">
    ...
  </div>

  <!-- Once + 键修饰符 -->
  <input @keyup.enter.once="submitOnFirstEnter" />
</template>
```

## 等效的手动实现

没有 `.once`，你需要手动跟踪和移除：

```vue
<script setup>
const hasHandled = ref(false)

function handleClickManually() {
  if (hasHandled.value) return
  hasHandled.value = true

  // 执行一次性操作
  doSomething()
}
</script>

<template>
  <!-- 手动方法 - 更冗长 -->
  <button @click="handleClickManually">点击</button>

  <!-- .once 方法 - 更简洁 -->
  <button @click.once="doSomething">点击</button>
</template>
```

## 何时不使用 .once

在以下情况下不要使用 `.once`：
- 您需要事件多次触发
- 您希望有条件地允许重复触发
- "once" 逻辑很复杂（改用手动 ref 跟踪）

```vue
<template>
  <!-- 不要：用户期望多次提交都能工作 -->
  <form @submit.once.prevent="handleSubmit">
    ...
  </form>

  <!-- 应该：允许重复提交 -->
  <form @submit.prevent="handleSubmit">
    ...
  </form>
</template>
```

## 参考
- [Vue.js 事件处理 - 事件修饰符](https://vuejs.org/guide/essentials/event-handling.html#event-modifiers)
- [Vue.js 组件事件](https://vuejs.org/guide/components/events.html)
