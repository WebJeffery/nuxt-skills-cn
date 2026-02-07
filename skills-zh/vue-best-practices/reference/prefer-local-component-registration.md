---
title: 优先使用本地组件注册而非全局注册
impact: MEDIUM
impactDescription: 全局注册阻止构建工具移除未使用的组件（tree-shaking），增加包大小。它还创建类似于全局变量的隐式依赖，使得难以跟踪组件的使用位置并在大型应用中定位其实现。
type: best-practice
tags: [vue3, component-registration, tree-shaking, performance, maintainability]
---

# 优先使用本地组件注册而非全局注册

**影响：中** - 全局注册阻止构建工具移除未使用的组件（tree-shaking），增加包大小。它还创建类似于全局变量的隐式依赖，使得难以跟踪组件的使用位置并在大型应用中定位其实现。

使用本地注册以获得更好的可维护性、更小的包和显式的依赖关系。

## 任务清单

- [ ] 默认使用本地注册（在每个组件中导入）
- [ ] 仅对真正无处不在的组件（图标、按钮、布局）保留全局注册
- [ ] 使用 `<script setup>` 时，只需导入组件 - 无需显式注册
- [ ] 审查现有的全局组件，并在可能的情况下迁移到本地注册

**错误：**
```javascript
// main.js - 全局注册许多组件
import { createApp } from 'vue'
import App from './App.vue'
import Card from './components/Card.vue'
import Modal from './components/Modal.vue'
import Table from './components/Table.vue'
import Pagination from './components/Pagination.vue'
import UserAvatar from './components/UserAvatar.vue'
import SearchBar from './components/SearchBar.vue'

const app = createApp(App)

// 错误：所有这些组件现在都在包中，即使未使用
app.component('Card', Card)
app.component('Modal', Modal)
app.component('Table', Table)
app.component('Pagination', Pagination)
app.component('UserAvatar', UserAvatar)
app.component('SearchBar', SearchBar)

app.mount('#app')
```

```vue
<!-- SomePage.vue - 仅使用 Card，但所有组件都被打包 -->
<template>
  <Card>
    <p>内容</p>
  </Card>
</template>

<script setup>
// 无导入 - 依赖全局注册
// 这使依赖不可见并损害 tree-shaking
</script>
```

**正确：**
```javascript
// main.js - 仅全局注册真正通用的组件
import { createApp } from 'vue'
import App from './App.vue'
import BaseIcon from './components/BaseIcon.vue'
import BaseButton from './components/BaseButton.vue'

const app = createApp(App)

// 仅真正通用的基础组件
app.component('BaseIcon', BaseIcon)
app.component('BaseButton', BaseButton)

app.mount('#app')
```

```vue
<!-- SomePage.vue - 显式本地导入 -->
<script setup>
// 正确：显式导入启用 tree-shaking
// 仅 Card 包含在此组件的包中
import Card from '@/components/Card.vue'
import UserAvatar from '@/components/UserAvatar.vue'
</script>

<template>
  <Card>
    <UserAvatar :user="currentUser" />
    <p>内容</p>
  </Card>
</template>
```

```vue
<!-- Options API 本地注册 -->
<script>
import Card from '@/components/Card.vue'
import UserAvatar from '@/components/UserAvatar.vue'

export default {
  components: {
    Card,
    UserAvatar
  }
}
</script>
```

## 何时全局注册是合适的

```javascript
// 在整个应用中使用的真正通用基础组件
import BaseIcon from './components/BaseIcon.vue'
import BaseButton from './components/BaseButton.vue'
import BaseInput from './components/BaseInput.vue'

// 具有受控范围的第三方组件库
import { Button, Input } from 'some-ui-library'

app.component('BaseIcon', BaseIcon)
app.component('BaseButton', BaseButton)
app.component('BaseInput', BaseInput)
```

## 本地注册的好处

| 方面 | 全局 | 本地 |
|--------|--------|-------|
| Tree-shaking | 不可能 | 完全支持 |
| 依赖跟踪 | 隐式/隐藏 | 显式导入 |
| 组件位置 | 难以找到 | 跟随导入路径 |
| 包大小 | 所有注册的组件 | 仅使用的组件 |
| 重构 | 破坏未知使用的风险 | 清晰的依赖图 |

## 参考
- [Vue.js 组件注册](https://vuejs.org/guide/components/registration.html)
