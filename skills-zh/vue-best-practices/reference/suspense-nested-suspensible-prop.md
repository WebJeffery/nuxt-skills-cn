# 对嵌套 Suspense 使用 suspensible 属性 (Vue 3.3+)

## 规则

嵌套 `<Suspense>` 组件时，在内部 Suspense 上使用 `suspensible` 属性以与父 Suspense 正确协调。没有此属性，嵌套的异步组件可能会在解决之前渲染空节点，而不是显示回退内容。

## 为什么这很重要

没有 `suspensible` 属性，内部 `<Suspense>` 被父元素视为同步组件。这导致:
- 异步解决期间短暂出现空节点
- 组件树更新时的多个补丁周期
- 令用户困惑的不一致的加载状态

## 不好的代码

```vue
<template>
  <Suspense>
    <component :is="DynamicAsyncOuter">
      <!-- DynamicAsyncInner 在解决之前渲染为空! -->
      <component :is="DynamicAsyncInner" />
    </component>

    <template #fallback>
      加载中...
    </template>
  </Suspense>
</template>
```

```vue
<template>
  <Suspense>
    <OuterLayout>
      <!-- 内部 Suspense 与父元素不协调 -->
      <Suspense>
        <AsyncWidget />
        <template #fallback>加载小部件中...</template>
      </Suspense>
    </OuterLayout>

    <template #fallback>
      加载布局中...
    </template>
  </Suspense>
</template>
```

## 好的代码

```vue
<template>
  <Suspense>
    <component :is="DynamicAsyncOuter">
      <!-- 带有 suspensible 属性的内部 Suspense -->
      <Suspense suspensible>
        <component :is="DynamicAsyncInner" />
        <template #fallback>
          <InnerLoadingState />
        </template>
      </Suspense>
    </component>

    <template #fallback>
      <OuterLoadingState />
    </template>
  </Suspense>
</template>
```

## 复杂布局示例

```vue
<template>
  <Suspense>
    <DashboardLayout>
      <template #sidebar>
        <!-- 侧边栏有自己的 Suspense 边界 -->
        <Suspense suspensible>
          <AsyncSidebar />
          <template #fallback>
            <SidebarSkeleton />
          </template>
        </Suspense>
      </template>

      <template #main>
        <!-- 主要内容有自己的 Suspense 边界 -->
        <Suspense suspensible>
          <AsyncMainContent :key="contentKey" />
          <template #fallback>
            <ContentSkeleton />
          </template>
        </Suspense>
      </template>

      <template #widgets>
        <!-- 每个小部件可以独立加载 -->
        <Suspense suspensible>
          <AsyncWidgets />
          <template #fallback>
            <WidgetsSkeleton />
          </template>
        </Suspense>
      </template>
    </DashboardLayout>

    <template #fallback>
      <FullPageLoader />
    </template>
  </Suspense>
</template>
```

## suspensible 如何工作

| 配置 | 行为 |
|--------------|----------|
| 没有 `suspensible` 属性 | 内部 Suspense 被视为同步；父元素不等待内部异步依赖 |
| 内部有 `suspensible` | 内部异步依赖冒泡到父 Suspense 进行协调加载 |

## 何时使用嵌套 Suspense

**使用带有 `suspensible` 的嵌套 Suspense** 当:
- 不同的页面部分应该显示自己的加载状态
- 你想要对哪些部分显示加载指示器进行细粒度控制
- 部分可以以不同的速度加载并应该独立更新

**避免嵌套 Suspense** 当:
- 整个组件的单个加载状态就足够了
- 复杂性不值得 UX 好处
- 你针对的是 3.3 之前的 Vue 版本

## 关键点

1. 嵌套 Suspense 组件时始终添加 `suspensible` 属性
2. 没有 `suspensible`，期望空节点和多个补丁周期
3. 此功能需要 Vue 3.3 或更高版本
4. 战略性地使用嵌套 Suspense 边界以获得更好的 UX，而不是到处使用
5. 每个 Suspense 可以有自己的回退，用于特定于部分的加载状态

## 参考

- [Vue.js Suspense 嵌套文档](https://vuejs.org/guide/built-ins/suspense#nested-suspense)
