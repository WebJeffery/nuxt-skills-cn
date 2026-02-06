# 键盘导航实现

## 为什么键盘导航重要

- 运动障碍用户无法使用鼠标
- 屏幕阅读器用户主要使用键盘
- 高级用户依赖键盘提高效率
- 移动设备也有外部键盘支持

## 基础键盘导航

### Tab 键导航

```vue
<template>
  <!-- ✅ 原生可交互元素自动支持 Tab -->
  <button>可聚焦按钮</button>
  <a href="/">可聚焦链接</a>
  <input type="text" />
  <select>
    <option>选项1</option>
  </select>
  <textarea></textarea>

  <!-- ❌ div 默认不可聚焦 -->
  <div @click="doSomething">不可聚焦</div>

  <!-- ✅ 添加 tabindex 使 div 可聚焦 -->
  <div
    tabindex="0"
    @click="doSomething"
    @keydown.enter="doSomething"
    @keydown.space.prevent="doSomething"
  >
    可聚焦 div
  </div>
</template>
```

### tabindex 值

```vue
<template>
  <!-- tabindex="0": 按文档顺序聚焦 -->
  <div tabindex="0">可聚焦元素</div>

  <!-- tabindex="-1": 只能通过程序聚焦,不参与 Tab 导航 -->
  <div tabindex="-1">程序聚焦元素</div>

  <!-- ❌ 避免使用 tabindex > 1 -->
  <div tabindex="100">跳过其他元素(不推荐)</div>

  <!-- 正确做法: 调整 DOM 顺序或使用 tabindex="0" -->
</template>
```

## 实现键盘交互

### 激活操作

```vue
<script setup lang="ts">
const handleClick = () => {
  console.log('激活')
}
</script>

<template>
  <!-- ✅ 使用原生按钮 -->
  <button @click="handleClick">
    点击我
  </button>

  <!-- 自定义可点击元素 -->
  <div
    role="button"
    tabindex="0"
    @click="handleClick"
    @keydown.enter="handleClick"
    @keydown.space.prevent="handleClick"
  >
    点击我
  </div>
</template>
```

### 键盘事件处理

```vue
<script setup lang="ts>
const handleKeydown = (event: KeyboardEvent) => {
  switch (event.key) {
    case 'Enter':
      // 确认
      break
    case ' ':
      // 激活(需要 preventDefault)
      event.preventDefault()
      break
    case 'Escape':
      // 取消/关闭
      break
    case 'ArrowUp':
      event.preventDefault()
      // 向上移动
      break
    case 'ArrowDown':
      event.preventDefault()
      // 向下移动
      break
    case 'ArrowLeft':
      event.preventDefault()
      // 向左移动
      break
    case 'ArrowRight':
      event.preventDefault()
      // 向右移动
      break
    case 'Home':
      event.preventDefault()
      // 跳到开始
      break
    case 'End':
      event.preventDefault()
      // 跳到末尾
      break
    case 'PageUp':
      event.preventDefault()
      // 向上翻页
      break
    case 'PageDown':
      event.preventDefault()
      // 向下翻页
      break
  }
}
</script>

<template>
  <div
    tabindex="0"
    @keydown="handleKeydown"
  >
    可键盘操作的元素
  </div>
</template>
```

## 常见模式

### 下拉菜单

```vue
<script setup lang="ts>
import { ref, onMounted, onUnmounted } from 'vue'

const isOpen = ref(false)
const activeIndex = ref(0)
const menuItems = [
  { id: 1, label: '新建', action: () => console.log('新建') },
  { id: 2, label: '打开', action: () => console.log('打开') },
  { id: 3, label: '保存', action: () => console.log('保存') },
  { id: 4, label: '退出', action: () => console.log('退出') },
]

const toggleMenu = () => {
  isOpen.value = !isOpen.value
  activeIndex.value = 0
}

const closeMenu = () => {
  isOpen.value = false
  activeIndex.value = -1
}

const handleMenuKeydown = (event: KeyboardEvent) => {
  if (!isOpen.value) {
    if (event.key === 'Enter' || event.key === ' ' || event.key === 'ArrowDown') {
      event.preventDefault()
      isOpen.value = true
    }
    return
  }

  switch (event.key) {
    case 'Escape':
      closeMenu()
      break
    case 'ArrowDown':
      event.preventDefault()
      activeIndex.value = (activeIndex.value + 1) % menuItems.length
      break
    case 'ArrowUp':
      event.preventDefault()
      activeIndex.value =
        (activeIndex.value - 1 + menuItems.length) % menuItems.length
      break
    case 'Home':
      event.preventDefault()
      activeIndex.value = 0
      break
    case 'End':
      event.preventDefault()
      activeIndex.value = menuItems.length - 1
      break
    case 'Enter':
    case ' ':
      event.preventDefault()
      menuItems[activeIndex.value].action()
      closeMenu()
      break
  }
}

const handleClickOutside = (event: MouseEvent) => {
  if (!event.target) return
  if (!(event.target as Element).closest('.dropdown-menu')) {
    closeMenu()
  }
}

onMounted(() => {
  document.addEventListener('click', handleClickOutside)
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
})
</script>

<template>
  <div class="dropdown-menu">
    <button
      @click="toggleMenu"
      @keydown="handleMenuKeydown"
      :aria-expanded="isOpen"
      aria-haspopup="true"
    >
      文件
    </button>

    <ul
      v-if="isOpen"
      role="menu"
      @keydown="handleMenuKeydown"
    >
      <li
        v-for="(item, index) in menuItems"
        :key="item.id"
        role="menuitem"
        :tabindex="activeIndex === index ? 0 : -1"
        :class="{ active: activeIndex === index }"
        @click="item.action(); closeMenu()"
      >
        {{ item.label }}
      </li>
    </ul>
  </div>
</template>
```

### 选项卡

```vue
<script setup lang="ts>
import { ref, watch } from 'vue'

const activeTab = ref('tab1')

const tabs = [
  { id: 'tab1', label: '首页' },
  { id: 'tab2', label: '产品' },
  { id: 'tab3', label: '关于' },
]

const activateTab = (tabId: string, direction?: number) => {
  const currentIndex = tabs.findIndex(t => t.id === activeTab.value)

  if (direction === -1) {
    // 向左
    const newIndex = (currentIndex - 1 + tabs.length) % tabs.length
    activeTab.value = tabs[newIndex].id
  } else if (direction === 1) {
    // 向右
    const newIndex = (currentIndex + 1) % tabs.length
    activeTab.value = tabs[newIndex].id
  } else {
    activeTab.value = tabId
  }
}

const handleTabKeydown = (event: KeyboardEvent, tabId: string) => {
  switch (event.key) {
    case 'ArrowLeft':
      event.preventDefault()
      activateTab(tabId, -1)
      break
    case 'ArrowRight':
      event.preventDefault()
      activateTab(tabId, 1)
      break
    case 'Home':
      event.preventDefault()
      activateTab(tabs[0].id)
      break
    case 'End':
      event.preventDefault()
      activateTab(tabs[tabs.length - 1].id)
      break
  }
}
</script>

<template>
  <div class="tabs">
    <div role="tablist">
      <button
        v-for="tab in tabs"
        :key="tab.id"
        :id="`tab-${tab.id}`"
        role="tab"
        :aria-selected="activeTab === tab.id"
        :tabindex="activeTab === tab.id ? 0 : -1"
        @click="activateTab(tab.id)"
        @keydown="handleTabKeydown($event, tab.id)"
      >
        {{ tab.label }}
      </button>
    </div>

    <div
      v-for="tab in tabs"
      :key="tab.id"
      :id="`panel-${tab.id}`"
      role="tabpanel"
      :aria-labelledby="`tab-${tab.id}`"
      :hidden="activeTab !== tab.id"
      :tabindex="activeTab === tab.id ? 0 : -1"
    >
      {{ tab.label }} 的内容
    </div>
  </div>
</template>
```

### 模态框焦点管理

```vue
<script setup lang="ts>
import { ref, watch, nextTick } from 'vue'

const isOpen = ref(false)
const modalRef = ref<HTMLElement>()
const previousActiveElement = ref<HTMLElement>()

const openModal = () => {
  previousActiveElement.value = document.activeElement as HTMLElement
  isOpen.value = true
}

const closeModal = () => {
  isOpen.value = false
}

watch(isOpen, async (newValue) => {
  if (newValue) {
    await nextTick()
    // 聚焦到模态框
    modalRef.value?.focus()

    // 禁用背景滚动
    document.body.style.overflow = 'hidden'
  } else {
    // 恢复焦点
    previousActiveElement.value?.focus()

    // 恢复背景滚动
    document.body.style.overflow = ''
  }
})

const trapFocus = (event: KeyboardEvent) => {
  if (event.key === 'Tab') {
    const focusableElements = modalRef.value?.querySelectorAll(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    ) as NodeListOf<HTMLElement>

    const firstElement = focusableElements[0]
    const lastElement = focusableElements[focusableElements.length - 1]

    if (event.shiftKey) {
      if (document.activeElement === firstElement) {
        lastElement?.focus()
        event.preventDefault()
      }
    } else {
      if (document.activeElement === lastElement) {
        firstElement?.focus()
        event.preventDefault()
      }
    }
  }

  if (event.key === 'Escape') {
    closeModal()
  }
}
</script>

<template>
  <div
    v-if="isOpen"
    class="modal"
    ref="modalRef"
    tabindex="-1"
    role="dialog"
    aria-modal="true"
    @keydown="trapFocus"
  >
    <h2>模态框标题</h2>
    <p>模态框内容...</p>
    <button @click="closeModal">关闭</button>
  </div>
</template>
```

### 跳过链接

```vue
<template>
  <!-- 跳到主要内容 -->
  <a
    href="#main-content"
    class="skip-link"
  >
    跳到主要内容
  </a>

  <!-- 跳到导航 -->
  <a
    href="#main-navigation"
    class="skip-link"
  >
    跳到导航
  </a>

  <header>
    <nav id="main-navigation">...</nav>
  </header>

  <main id="main-content">
    <!-- 主要内容 -->
  </main>
</template>

<style>
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  padding: 8px;
  background: #000;
  color: #fff;
  text-decoration: none;
  z-index: 100;
}

.skip-link:focus {
  top: 0;
}
</style>
```

## 焦点样式

```css
/* 可见焦点指示器 */
button:focus,
a:focus,
input:focus,
textarea:focus,
select:focus,
[tabindex]:focus {
  outline: 2px solid #4A90E2;
  outline-offset: 2px;
}

/* 移除默认但保持可访问 */
button:focus-visible {
  outline: 2px solid #4A90E2;
  outline-offset: 2px;
}

/* 只为键盘导航显示焦点 */
button:focus:not(:focus-visible) {
  outline: none;
}
```

## 键盘快捷键

```vue
<script setup lang="ts>
import { onMounted, onUnmounted } from 'vue'

const handleGlobalKeydown = (event: KeyboardEvent) => {
  // Ctrl/Cmd + K: 打开搜索
  if ((event.ctrlKey || event.metaKey) && event.key === 'k') {
    event.preventDefault()
    openSearch()
  }

  // / : 聚焦搜索框(不在输入框时)
  if (event.key === '/' && !(event.target instanceof HTMLInputElement)) {
    event.preventDefault()
    focusSearch()
  }

  // Escape: 关闭模态框/下拉菜单
  if (event.key === 'Escape') {
    closeAll()
  }
}

onMounted(() => {
  document.addEventListener('keydown', handleGlobalKeydown)
})

onUnmounted(() => {
  document.removeEventListener('keydown', handleGlobalKeydown)
})
</script>

<template>
  <div>
    <!-- 显示快捷键提示 -->
    <button aria-keyshortcuts="Ctrl+K">
      搜索 <kbd>Ctrl</kbd> + <kbd>K</kbd>
    </button>
  </div>
</template>
```

## 测试清单

- [ ] 所有交互元素可通过 Tab 键访问
- [ ] Tab 顺序符合逻辑
- [ ] 焦点指示器清晰可见
- [ ] 可通过 Enter/Space 激活按钮
- [ ] 可通过 Escape 关闭模态框/菜单
- [ ] 方向键可用于列表/菜单导航
- [ ] Home/End 可跳到列表首尾
- [ ] 模态框打开时焦点被管理
- [ ] 模态框关闭时焦点恢复到打开位置
- [ ] 跳过链接可用
