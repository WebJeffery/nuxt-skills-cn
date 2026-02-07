# Suspense 仅在根节点替换时恢复到待定状态

## 规则

一旦解决，`<Suspense>` 仅在 `#default` 插槽的根节点被替换时才恢复到待定状态。树中嵌套的更深层的新异步依赖**不会**导致 `<Suspense>` 恢复到待定状态。

## 为什么这很重要

这是一个常见的困惑来源。开发人员期望 Suspense 在树中的任何异步组件开始加载时显示回退，但它仅在根级别更改时触发。嵌套的异步更新会静默完成，而不显示任何加载指示器。

## 不好的代码

```vue
<script setup>
import { ref } from 'vue'

const activeTab = ref('dashboard')
</script>

<template>
  <Suspense>
    <!-- 此包装器始终是相同的组件 -->
    <TabContainer>
      <!-- 选项卡更改不会触发 Suspense 回退! -->
      <AsyncDashboard v-if="activeTab === 'dashboard'" />
      <AsyncSettings v-else-if="activeTab === 'settings'" />
      <AsyncProfile v-else-if="activeTab === 'profile'" />
    </TabContainer>

    <template #fallback>
      加载中... <!-- 仅在初始加载时显示 -->
    </template>
  </Suspense>
</template>
```

## 好的代码

### 解决方案 1: 使用 :key 强制根替换

```vue
<script setup>
import { ref, defineAsyncComponent } from 'vue'

const activeTab = ref('dashboard')

const tabs = {
  dashboard: defineAsyncComponent(() => import('./Dashboard.vue')),
  settings: defineAsyncComponent(() => import('./Settings.vue')),
  profile: defineAsyncComponent(() => import('./Profile.vue'))
}
</script>

<template>
  <Suspense>
    <!-- 键更改强制根替换，触发 Suspense -->
    <component :is="tabs[activeTab]" :key="activeTab" />

    <template #fallback>
      加载中...
    </template>
  </Suspense>
</template>
```

### 解决方案 2: 带有 suspensible 属性的嵌套 Suspense (Vue 3.3+)

```vue
<template>
  <Suspense>
    <TabContainer>
      <!-- 内部 Suspense 处理嵌套的异步组件 -->
      <Suspense suspensible>
        <AsyncDashboard v-if="activeTab === 'dashboard'" />
        <AsyncSettings v-else-if="activeTab === 'settings'" />
        <AsyncProfile v-else-if="activeTab === 'profile'" />

        <template #fallback>
          <TabLoadingSpinner />
        </template>
      </Suspense>
    </TabContainer>

    <template #fallback>
      <PageLoadingSkeleton />
    </template>
  </Suspense>
</template>
```

### 解决方案 3: 嵌套内容的手动加载状态

```vue
<script setup>
import { ref, watch } from 'vue'

const activeTab = ref('dashboard')
const isTabLoading = ref(false)

watch(activeTab, () => {
  isTabLoading.value = true
})

const onTabLoaded = () => {
  isTabLoading.value = false
}
</script>

<template>
  <Suspense>
    <TabContainer>
      <div v-if="isTabLoading" class="tab-loading">
        加载选项卡中...
      </div>
      <AsyncTab
        v-else
        :tab="activeTab"
        @vue:mounted="onTabLoaded"
      />
    </TabContainer>

    <template #fallback>
      初始加载中...
    </template>
  </Suspense>
</template>
```

## 关键点

1. Suspense 跟踪根节点标识，而不是整个子树
2. 使用 `:key` 强制根节点替换，当你需要 Suspense 重新触发时
3. 对于 Vue 3.3+，带有 `suspensible` 属性的嵌套 `<Suspense>` 处理深度嵌套的异步组件
4. 考虑手动加载状态以对嵌套内容进行细粒度控制
5. 没有 `suspensible` 属性，内部 Suspense 被视为同步并导致多个补丁周期

## 参考

- [Vue.js Suspense 文档](https://vuejs.org/guide/built-ins/suspense#nested-suspense)
