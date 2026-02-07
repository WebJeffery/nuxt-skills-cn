---
title: 组合式 API 生成更小、更高效的打包文件
impact: LOW
impactDescription: 理解这一点有助于为性能敏感的项目证明采用组合式 API 的合理性
type: efficiency
tags: [vue3, composition-api, bundle-size, minification, performance]
---

# 组合式 API 生成更小、更高效的打包文件

**影响:低** - 组合式 API 比选项 API 更易于压缩,从而产生更小的生产包和更少的运行时开销。这是一个有益的副作用,而不是选择组合式 API 的主要原因。

在 `<script setup>` 中,变量和函数的名称可以由压缩器安全地缩短,因为它们是组件作用域的局部变量。选项 API 属性(methods、computed、data)通过 `this` 上的字符串键访问,这些键无法被压缩。

## 任务清单

- [ ] 优先使用 `<script setup>` 以获得最佳压缩效果
- [ ] 理解组合式 API 避免了 `this` 代理开销
- [ ] 对于不使用选项 API 的项目,考虑启用编译时标志以删除选项 API 代码
- [ ] 注意使用选项 API 的库无论如何都会包含该代码

**打包大小差异:**
```javascript
// 选项 API - 压缩前
export default {
  data() {
    return {
      userAuthenticated: false,
      currentUserProfile: null,
      navigationMenuOpen: false
    }
  },
  computed: {
    displayUserName() {
      return this.currentUserProfile?.name || 'Guest'
    }
  },
  methods: {
    handleUserAuthentication() {
      this.userAuthenticated = true
    }
  }
}

// 选项 API - 压缩后(属性名称保留)
export default {
  data() {
    return {
      userAuthenticated: false,        // 无法缩短
      currentUserProfile: null,         // 无法缩短
      navigationMenuOpen: false         // 无法缩短
    }
  },
  computed: {
    displayUserName() {                 // 无法缩短
      return this.currentUserProfile?.name || 'Guest'
    }
  },
  methods: {
    handleUserAuthentication() {        // 无法缩短
      this.userAuthenticated = true
    }
  }
}
```

```javascript
// 组合式 API - 压缩前
<script setup>
import { ref, computed } from 'vue'

const userAuthenticated = ref(false)
const currentUserProfile = ref(null)
const navigationMenuOpen = ref(false)

const displayUserName = computed(() =>
  currentUserProfile.value?.name || 'Guest'
)

function handleUserAuthentication() {
  userAuthenticated.value = true
}
</script>

// 组合式 API - 压缩后
<script setup>
import { ref as r, computed as c } from 'vue'

const a = r(false)      // userAuthenticated -> a
const b = r(null)       // currentUserProfile -> b
const d = r(false)      // navigationMenuOpen -> d

const e = c(() => b.value?.name || 'Guest')  // displayUserName -> e

function f() { a.value = true }  // handleUserAuthentication -> f
</script>
```

## 运行时性能

```javascript
// 选项 API - 每个属性访问都通过代理
export default {
  methods: {
    doSomething() {
      // this.count 触发代理 get 拦截器
      // this.items.push 触发代理 get 拦截器
      console.log(this.count)  // 代理开销
      this.items.push(item)    // 代理开销
    }
  }
}

// 组合式 API - 直接变量访问
<script setup>
const count = ref(0)
const items = ref([])

function doSomething() {
  // 直接变量访问 - 变量本身没有代理间接访问
  console.log(count.value)
  items.value.push(item)
}
</script>
```

## 为纯组合式 API 项目删除选项 API

```javascript
// vite.config.js - 仅适用于完全使用组合式 API 的项目
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  define: {
    // 这将从打包文件中删除选项 API 支持
    __VUE_OPTIONS_API__: false
  }
})

// 警告: 这将破坏任何(包括来自库的)
// 使用选项 API 的组件。仅当您确定所有组件
// 都使用组合式 API 时才使用。
```

## 何时这很重要

打包大小差异通常是:
- **小组件**: 差异可忽略
- **大型应用程序**: 使用组合式 API 小 10-15%
- **禁用选项 API 标志**: 额外节省 5-10%

主要为了代码组织和逻辑重用的好处而选择组合式 API。打包大小的改进是一个不错的奖励,而不是切换的主要原因。

## 参考
- [组合式 API FAQ - 更小的生产包](https://vuejs.org/guide/extras/composition-api-faq.html#smaller-production-bundle-and-less-overhead)
- [Vue 3 构建功能标志](https://github.com/vuejs/core/tree/main/packages/vue#bundler-build-feature-flags)
