---
title: 永远不要变异计算属性返回值
impact: HIGH
impactDescription: 变异计算值会导致静默失败和丢失更改
type: capability
tags: [vue3, computed, reactivity, immutability, common-mistake]
---

# 永远不要变异计算属性返回值

**影响：高** - 计算属性的返回值是派生状态 - 一个临时快照。变异此值会导致难以调试的错误。

**重要：** 当计算缓存保持有效时，变异确实会持续，但在重新计算时会丢失。危险在于不可预测的缓存失效时机 - 对计算依赖项的任何更改都会触发重新计算，静默地丢弃你的变异。这使得错误间歇性且难以重现。

每次源状态更改时，都会创建新的快照。变异快照是没有意义的，因为它会在下次重新计算时被丢弃。

## 任务清单

- [ ] 将计算返回值视为只读
- [ ] 更新源状态而不是计算值
- [ ] 如果需要双向绑定，使用可写计算属性
- [ ] 避免在计算数组上使用数组变异方法（push、pop、splice、reverse、sort）

**错误：**
```vue
<script setup>
import { ref, computed } from 'vue'

const books = ref(['Vue Guide', 'React Handbook'])

const publishedBooks = computed(() => {
  return books.value.filter(book => book.includes('Guide'))
})

function addBook() {
  // 错误：变异计算值 - 更改将丢失！
  publishedBooks.value.push('New Book')
}

// 错误：变异计算数组
const sortedBooks = computed(() => books.value.filter(b => b))

function reverseBooks() {
  // 错误：这变异了计算快照
  sortedBooks.value.reverse()
}
</script>
```

```vue
<script>
export default {
  data() {
    return {
      author: {
        name: 'John',
        books: ['Book A', 'Book B']
      }
    }
  },
  computed: {
    authorBooks() {
      return this.author.books
    }
  },
  methods: {
    addBook() {
      // 错误：变异计算值
      this.authorBooks.push('New Book')
    }
  }
}
</script>
```

**正确：**
```vue
<script setup>
import { ref, computed } from 'vue'

const books = ref(['Vue Guide', 'React Handbook'])

const publishedBooks = computed(() => {
  return books.value.filter(book => book.includes('Guide'))
})

function addBook(bookName) {
  // 正确：更新源状态
  books.value.push(bookName)
}

// 正确：在变异之前创建副本以进行显示
const sortedBooks = computed(() => {
  return [...books.value].sort()  // 在排序之前展开以创建副本
})

const reversedBooks = computed(() => {
  return [...books.value].reverse()  // 在反转之前展开以创建副本
})
</script>
```

```vue
<script>
export default {
  data() {
    return {
      author: {
        name: 'John',
        books: ['Book A', 'Book B']
      }
    }
  },
  computed: {
    authorBooks() {
      return this.author.books
    }
  },
  methods: {
    addBook(bookName) {
      // 正确：更新源状态
      this.author.books.push(bookName)
    }
  }
}
</script>
```

## 用于双向绑定的可写计算属性

如果你确实需要"设置"计算值，请使用可写计算属性：

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

// 带有 getter 和 setter 的可写计算属性
const fullName = computed({
  get() {
    return `${firstName.value} ${lastName.value}`
  },
  set(newValue) {
    // 根据新值更新源状态
    const parts = newValue.split(' ')
    firstName.value = parts[0] || ''
    lastName.value = parts[1] || ''
  }
})

// 现在这是有效的：
fullName.value = 'Jane Smith'  // 更新 firstName 和 lastName
</script>
```

## 参考
- [Vue.js 计算属性 - 避免变异计算值](https://vuejs.org/guide/essentials/computed.html#avoid-mutating-computed-value)
- [Vue.js 计算属性 - 可写计算属性](https://vuejs.org/guide/essentials/computed.html#writable-computed)
