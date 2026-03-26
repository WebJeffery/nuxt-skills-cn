---
name: core-new-apis
description: Vue 3 响应式系统、生命周期钩子和 composable 模式
---

# 响应式、生命周期 & Composables

## 响应式

### ref vs shallowRef

```ts
import { ref, shallowRef } from 'vue'

// ref - 深度响应式(跟踪嵌套更改)
const user = ref({ name: 'John', profile: { age: 30 } })
user.value.profile.age = 31  // 触发响应式

// shallowRef - 仅 .value 赋值触发响应式(更好的性能)
const data = shallowRef({ items: [] })
data.value.items.push('new')  // 不触发响应式
data.value = { items: ['new'] }  // 触发响应式
```

**优先使用 `shallowRef`** 用于大型数据结构或不需要深度响应式时。

### computed

```ts
import { ref, computed } from 'vue'

const count = ref(0)

// 只读计算属性
const doubled = computed(() => count.value * 2)

// 可写计算属性
const plusOne = computed({
  get: () => count.value + 1,
  set: (val) => { count.value = val - 1 }
})
```

### reactive & readonly

```ts
import { reactive, readonly } from 'vue'

const state = reactive({ count: 0, nested: { value: 1 } })
state.count++  // 响应式

const readonlyState = readonly(state)
readonlyState.count++  // 警告,阻止变异
```

注意: `reactive()` 在解构时失去响应式。使用 `ref()` 或 `toRefs()`。

## 侦听器

### watch

```ts
import { ref, watch } from 'vue'

const count = ref(0)

// 侦听单个 ref
watch(count, (newVal, oldVal) => {
  console.log(`从 ${oldVal} 更改为 ${newVal}`)
})

// 侦听 getter
watch(
  () => props.id,
  (id) => fetchData(id),
  { immediate: true }
)

// 侦听多个源
watch([firstName, lastName], ([first, last]) => {
  fullName.value = `${first} ${last}`
})

// 深度侦听带深度限制(Vue 3.5+)
watch(state, callback, { deep: 2 })

// 一次性(Vue 3.4+)
watch(source, callback, { once: true })
```

### watchEffect

立即运行并自动跟踪依赖。

```ts
import { ref, watchEffect, onWatcherCleanup } from 'vue'

const id = ref(1)

watchEffect(async () => {
  const controller = new AbortController()
  
  // 在重新运行或卸载时清理(Vue 3.5+)
  onWatcherCleanup(() => controller.abort())
  
  const res = await fetch(`/api/${id.value}`, { signal: controller.signal })
  data.value = await res.json()
})

// 暂停/恢复(Vue 3.5+)
const { pause, resume, stop } = watchEffect(() => {})
pause()
resume()
stop()
```

### 刷新时机

```ts
// 'pre'(默认) - 组件更新之前
// 'post' - 组件更新之后(访问更新的 DOM)
// 'sync' - 立即,谨慎使用

watch(source, callback, { flush: 'post' })
watchPostEffect(() => {})  // flush: 'post' 的别名
```

## 生命周期钩子

```ts
import {
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted,
  onErrorCaptured,
  onActivated,      // KeepAlive
  onDeactivated,    // KeepAlive
  onServerPrefetch  // 仅 SSR
} from 'vue'

onMounted(() => {
  console.log('DOM 已就绪')
})

onUnmounted(() => {
  // 清理计时器、侦听器等
})

// 错误边界
onErrorCaptured((err, instance, info) => {
  console.error(err)
  return false  // 停止传播
})
```

## Effect Scope

将响应式效果分组以便批量清理。

```ts
import { effectScope, onScopeDispose } from 'vue'

const scope = effectScope()

scope.run(() => {
  const count = ref(0)
  const doubled = computed(() => count.value * 2)
  
  watch(count, () => console.log(count.value))
  
  // 当作用域停止时清理
  onScopeDispose(() => {
    console.log('作用域已清理')
  })
})

// 清理所有效果
scope.stop()
```

## Composables

Composables 是使用 Composition API 封装有状态逻辑的函数。

### 命名约定

- 以 `use` 开头: `useMouse`、`useFetch`、`useCounter`

### 模式

```ts
// composables/useMouse.ts
import { ref, onMounted, onUnmounted } from 'vue'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  const update = (e: MouseEvent) => {
    x.value = e.pageX
    y.value = e.pageY
  }

  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  return { x, y }
}
```

### 接受响应式输入

使用 `toValue()` (Vue 3.3+) 来规范化 refs、getters 或普通值。

```ts
import { ref, watchEffect, toValue, type MaybeRefOrGetter } from 'vue'

export function useFetch(url: MaybeRefOrGetter<string>) {
  const data = ref(null)
  const error = ref(null)

  watchEffect(async () => {
    data.value = null
    error.value = null
    
    try {
      const res = await fetch(toValue(url))
      data.value = await res.json()
    } catch (e) {
      error.value = e
    }
  })

  return { data, error }
}

// 使用 - 都可以工作:
useFetch('/api/users')
useFetch(urlRef)
useFetch(() => `/api/users/${props.id}`)
```

### 返回 Refs(而非响应式对象)

始终返回带有 refs 的普通对象以保持解构兼容性。

```ts
// 好 - 解构时保持响应式
return { x, y }

// 坏 - 解构时失去响应式
return reactive({ x, y })
```

<!--
Source references:
- https://vuejs.org/api/reactivity-core.html
- https://vuejs.org/api/reactivity-advanced.html
- https://vuejs.org/api/composition-api-lifecycle.html
- https://vuejs.org/guide/reusability/composables.html
-->
