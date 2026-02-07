---
title: 组合式 API 和选项 API 可以在同一组件中共存
impact: LOW
impactDescription: 理解共存有助于逐步迁移和库集成
type: best-practice
tags: [vue3, composition-api, options-api, migration, interoperability]
---

# 组合式 API 和选项 API 可以在同一组件中共存

**影响:低** - Vue 3 允许通过 `setup()` 选项在同一组件中使用两种 API。这对于现有选项 API 代码库的逐步迁移或将组合式 API 库集成到选项 API 组件中很有用。

然而,这应该是一个过渡模式。对于新代码,选择一种 API 风格并坚持使用它。

## 任务清单

- [ ] 仅在迁移现有代码或集成库时混合 API
- [ ] 与选项 API 混合时使用 `setup()` 选项(而不是 `<script setup>`)
- [ ] 从 setup 返回 refs/reactive 以使它们可用于选项 API 代码
- [ ] 避免长期混合 - 计划最终完全迁移
- [ ] 理解选项 API `this` 在 `setup()` 中不可用

**在选项 API 组件中使用组合式 API:**
```javascript
import { ref, computed, onMounted } from 'vue'
import { useExternalLibrary } from 'some-composition-library'

export default {
  // 选项 API 部分
  data() {
    return {
      legacyData: 'from options api'
    }
  },

  computed: {
    legacyComputed() {
      // 可以访问选项 API 数据和 setup() 返回的值
      return this.legacyData + ' - ' + this.newFeatureData
    }
  },

  methods: {
    legacyMethod() {
      // 可以调用来自两种 API 的方法
      this.composableMethod()
    }
  },

  // 通过 setup() 的组合式 API
  setup() {
    // 使用没有选项 API 等效项的组合式库
    const { data: libraryData, doSomething } = useExternalLibrary()

    // 使用组合式 API 创建新的响应式状态
    const newFeatureData = ref('from composition api')

    const newComputed = computed(() =>
      newFeatureData.value.toUpperCase()
    )

    function composableMethod() {
      newFeatureData.value = 'updated'
    }

    // 重要: 返回值以使它们可用于选项 API
    return {
      libraryData,
      doSomething,
      newFeatureData,
      newComputed,
      composableMethod
    }
  }
}
```

**常见迁移模式:**
```javascript
// 步骤 1: 原始选项 API 组件
export default {
  data() {
    return {
      users: [],
      loading: false
    }
  },
  methods: {
    async fetchUsers() {
      this.loading = true
      this.users = await api.getUsers()
      this.loading = false
    }
  },
  mounted() {
    this.fetchUsers()
  }
}

// 步骤 2: 将逻辑提取到组合式函数,通过 setup() 使用
import { useUsers } from '@/composables/useUsers'

export default {
  data() {
    return {
      // 在迁移期间保留一些选项 API 数据
      selectedUserId: null
    }
  },

  computed: {
    selectedUser() {
      // 混合选项 API 计算和组合式 API 数据
      return this.users.find(u => u.id === this.selectedUserId)
    }
  },

  setup() {
    // 组合式 API 中的新逻辑
    const { users, loading, fetchUsers } = useUsers()
    return { users, loading, fetchUsers }
  },

  mounted() {
    this.fetchUsers()  // 从 setup() 可用
  }
}

// 步骤 3: 完全迁移到 <script setup>
<script setup>
import { ref, computed, onMounted } from 'vue'
import { useUsers } from '@/composables/useUsers'

const { users, loading, fetchUsers } = useUsers()
const selectedUserId = ref(null)

const selectedUser = computed(() =>
  users.value.find(u => u.id === selectedUserId.value)
)

onMounted(() => fetchUsers())
</script>
```

**重要限制:**
```javascript
export default {
  data() {
    return { optionsData: 'hello' }
  },

  setup(props, context) {
    // 错误: 'this' 在 setup() 中不可用!
    console.log(this.optionsData)  // undefined!

    // 正确: 通过参数访问 props 和 context
    console.log(props.someProp)
    console.log(context.attrs)
    console.log(context.emit)

    // 要从 setup 访问选项 API 数据,
    // 通常不能 - 它们在不同的作用域中
    // 但选项 API 可以访问 setup 的返回值

    return { /* ... */ }
  }
}
```

## 何时使用此模式

- **迁移大型代码库**: 逐段迁移,而无需重写所有内容
- **集成库**: 一些库(如 VueUse)仅是组合式 API
- **团队过渡**: 让团队逐步学习组合式 API
- **需要一个组合式函数的选项 API 组件**: 快速集成

## 何时不使用此模式

- **新组件**: 从一开始就使用 `<script setup>`
- **简单组件**: 不值得心理开销
- **长期**: 计划完全迁移;混合会增加复杂性

## 参考
- [组合式 API FAQ - 使用两种 API](https://vuejs.org/guide/extras/composition-api-faq.html#can-i-use-both-apis-in-the-same-component)
- [setup() 选项](https://vuejs.org/api/composition-api-setup.html)
