---
title: Vue 3 自定义指令钩子从 Vue 2 重命名
impact: HIGH
impactDescription: 在 Vue 3 中使用 Vue 2 钩子名称会导致指令静默失败
type: gotcha
tags: [vue3, vue2, migration, directives, hooks, breaking-change]
---

# Vue 3 自定义指令钩子从 Vue 2 重命名

**影响：高** - Vue 3 重命名了所有自定义指令生命周期钩子以与组件生命周期钩子对齐。使用 Vue 2 钩子名称会导致你的指令静默失败，因为钩子不会被调用。此外，`update` 钩子被完全移除。

这是一个破坏性更改，在从 Vue 2 迁移到 Vue 3 时需要更新所有自定义指令。

## 任务清单

- [ ] 将 `bind` 重命名为 `beforeMount`
- [ ] 将 `inserted` 重命名为 `mounted`
- [ ] 将 `update` 替换为 `beforeUpdate` 或 `updated`（update 被移除）
- [ ] 将 `componentUpdated` 重命名为 `updated`
- [ ] 将 `unbind` 重命名为 `unmounted`
- [ ] 如果你需要旧的 `update` 行为，添加 `beforeUpdate`

## 钩子名称映射

| Vue 2           | Vue 3          |
|-----------------|----------------|
| `bind`          | `beforeMount`  |
| `inserted`      | `mounted`      |
| `update`        | **已移除**    |
| `componentUpdated` | `updated`   |
| `unbind`        | `unmounted`    |
| (无)          | `created`      |
| (无)          | `beforeUpdate` |
| (无)          | `beforeUnmount`|

**Vue 2（旧）：**
```javascript
// Vue 2 指令 - 在 Vue 3 中不起作用
Vue.directive('demo', {
  bind(el, binding, vnode) {
    // 当指令首次绑定到元素时调用
  },
  inserted(el, binding, vnode) {
    // 当元素插入到父元素时调用
  },
  update(el, binding, vnode, oldVnode) {
    // 在每次 VNode 更新时调用（在 Vue 3 中已移除）
  },
  componentUpdated(el, binding, vnode, oldVnode) {
    // 在组件和子组件更新后调用
  },
  unbind(el, binding, vnode) {
    // 当指令从元素解绑时调用
  }
})
```

**Vue 3（新）：**
```javascript
// Vue 3 指令 - 正确的钩子名称
app.directive('demo', {
  created(el, binding, vnode) {
    // 新增：在元素的属性或事件监听器应用之前调用
  },
  beforeMount(el, binding, vnode) {
    // 原名：bind
  },
  mounted(el, binding, vnode) {
    // 原名：inserted
  },
  beforeUpdate(el, binding, vnode, prevVnode) {
    // 新增：在元素本身更新之前调用
  },
  updated(el, binding, vnode, prevVnode) {
    // 原名：componentUpdated
    // 注意：'update' 被移除了 - 使用此钩子或 beforeUpdate 代替
  },
  beforeUnmount(el, binding, vnode) {
    // 新增：在元素卸载之前调用
  },
  unmounted(el, binding, vnode) {
    // 原名：unbind
  }
})
```

## 迁移示例

### 简单的焦点指令
```javascript
// Vue 2
Vue.directive('focus', {
  inserted(el) {
    el.focus()
  }
})

// Vue 3
app.directive('focus', {
  mounted(el) {
    el.focus()
  }
})
```

### 带清理的指令
```javascript
// Vue 2
Vue.directive('click-outside', {
  bind(el, binding) {
    el._handler = (e) => {
      if (!el.contains(e.target)) binding.value(e)
    }
    document.addEventListener('click', el._handler)
  },
  unbind(el) {
    document.removeEventListener('click', el._handler)
  }
})

// Vue 3
app.directive('click-outside', {
  beforeMount(el, binding) {  // 或 mounted
    el._handler = (e) => {
      if (!el.contains(e.target)) binding.value(e)
    }
    document.addEventListener('click', el._handler)
  },
  unmounted(el) {
    document.removeEventListener('click', el._handler)
  }
})
```

### 带更新的指令
```javascript
// Vue 2 - 使用 update 钩子
Vue.directive('color', {
  bind(el, binding) {
    el.style.color = binding.value
  },
  update(el, binding) {
    // 在每次 VNode 更新时调用
    el.style.color = binding.value
  }
})

// Vue 3 - update 被移除，使用函数简写或 updated
app.directive('color', (el, binding) => {
  // 函数简写：在 mounted 和 updated 时都会调用
  el.style.color = binding.value
})

// 或使用对象语法
app.directive('color', {
  mounted(el, binding) {
    el.style.color = binding.value
  },
  updated(el, binding) {
    // 使用 updated 代替 update
    el.style.color = binding.value
  }
})
```

## 为什么 `update` 被移除

在 Vue 2 中，`update` 在每次 VNode 更新时调用（在子组件更新之前），而 `componentUpdated` 在之后调用。这种区别令人困惑，并且很少需要。在 Vue 3 中：

- `beforeUpdate` 在元素更新之前调用
- `updated` 在元素及其所有子组件更新之后调用

```javascript
// Vue 3 - 如果你需要更新前和更新后
app.directive('track-updates', {
  beforeUpdate(el, binding) {
    console.log('更新前，旧值：', binding.oldValue)
  },
  updated(el, binding) {
    console.log('更新后，新值：', binding.value)
  }
})
```

## vnode 结构变化

在 Vue 3 中，`vnode` 和 `prevVnode` 参数也有不同的结构：

```javascript
// Vue 2
{
  update(el, binding, vnode, oldVnode) {
    // vnode.context 是组件实例
    console.log(vnode.context)
  }
}

// Vue 3
{
  updated(el, binding, vnode, prevVnode) {
    // 使用 binding.instance 代替 vnode.context
    console.log(binding.instance)
  }
}
```

## 参考
- [Vue 3 迁移指南 - 自定义指令](https://v3-migration.vuejs.org/breaking-changes/custom-directives)
- [Vue.js 自定义指令 - 指令钩子](https://vuejs.org/guide/reusability/custom-directives#directive-hooks)
