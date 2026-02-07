# Suspense 回退在恢复时不立即显示

## 规则

当 Suspense 从已解决状态恢复到待定状态时，回退内容**不会**立即显示。相反，之前的 `#default` 内容在等待新内容解决时保持可见。使用 `timeout` 属性来控制回退何时出现。

## 为什么这很重要

此行为防止快速异步操作的 UI 闪烁，但当加载时间比预期长时可能会令用户困惑。如果不理解这一点，开发人员可能会认为他们的回退内容损坏或 Suspense 没有正常工作。

## 不好的代码

```vue
<template>
  <!-- 当内容更改时回退不会立即显示 -->
  <Suspense>
    <component :is="currentView" :key="viewKey" />

    <template #fallback>
      <!-- 对于快速加载这可能永远不会出现 -->
      <LoadingSpinner />
    </template>
  </Suspense>
</template>
```

## 好的代码

### 解决方案 1: 使用 timeout="0" 立即显示回退

```vue
<template>
  <!-- 回退在任何待定状态下立即显示 -->
  <Suspense timeout="0">
    <component :is="currentView" :key="viewKey" />

    <template #fallback>
      <LoadingSpinner />
    </template>
  </Suspense>
</template>
```

### 解决方案 2: 使用适当的超时以实现 UX 平衡

```vue
<template>
  <!-- 等待 200ms 后显示回退，以避免快速加载时的闪烁 -->
  <Suspense :timeout="200">
    <component :is="currentView" :key="viewKey" />

    <template #fallback>
      <LoadingSpinner />
    </template>
  </Suspense>
</template>
```

### 解决方案 3: 使用过渡实现平滑状态更改

```vue
<template>
  <RouterView v-slot="{ Component }">
    <template v-if="Component">
      <Transition mode="out-in">
        <Suspense :timeout="100">
          <component :is="Component" />

          <template #fallback>
            <div class="route-loading">
              <LoadingSpinner />
            </div>
          </template>
        </Suspense>
      </Transition>
    </template>
  </RouterView>
</template>
```

## 超时行为摘要

| 超时值 | 行为 |
|---------------|----------|
| 未指定 | 之前的内容显示直到新内容解决（恢复时没有回退） |
| `timeout="0"` | 进入待定状态时立即显示回退 |
| `timeout="200"` | 等待 200ms，如果仍然待定则显示回退 |

## 何时使用每种方法

**没有超时（默认）**:
- 当异步操作通常很快（<100ms）时
- 当在加载期间显示过时内容可接受时
- 为了防止快速导航时的闪烁

**timeout="0"**:
- 当你总是想要显示加载反馈时
- 对于通常需要明显时间的操作
- 当过时内容会令人困惑时

**带有延迟的超时（例如，200ms）**:
- 大多数情况下两全其美
- 避免快速加载时的闪烁
- 仍然为较慢的操作显示加载

## 关键点

1. 默认的 Suspense 行为优先显示过时内容而不是回退
2. 如果你总是想要加载指示器，使用 `timeout="0"`
3. 根据预期的异步操作持续时间选择超时值
4. 与 `<Transition>` 结合使用以获得更平滑的状态更改
5. 此行为是为了改善感知性能而设计的

## 参考

- [Vue.js Suspense 文档](https://vuejs.org/guide/built-ins/suspense#nested-suspense)
