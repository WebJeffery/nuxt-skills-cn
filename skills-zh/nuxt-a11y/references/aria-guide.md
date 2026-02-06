# ARIA 属性完整指南

## ARIA 是什么

ARIA (Accessible Rich Internet Applications) 是一组属性,用于弥补 HTML 在动态内容和高级 UI 组件方面的无障碍缺陷。

## 核心原则

**第一规则: 如果你可以使用原生 HTML 元素或属性,就不要使用 ARIA**

```vue
<!-- ❌ 错误 -->
<div role="button" @click="doSomething">点击</div>

<!-- ✅ 正确 -->
<button @click="doSomething">点击</button>
```

## ARIA 角色(roles)

什么是ARIA 角色：描述页面或页面组件的用途。


### Landmark 角色

```vue
<template>
  <!-- 页面级地标 -->
  <header role="banner">
    <h1>网站标题</h1>
  </header>

  <nav role="navigation" aria-label="主导航">
    <!-- 导航链接 -->
  </nav>

  <main role="main">
    <!-- 主要内容 -->
  </main>

  <aside role="complementary">
    <!-- 侧边栏 -->
  </aside>

  <footer role="contentinfo">
    <!-- 页脚信息 -->
  </footer>

  <!-- 搜索区域 -->
  <div role="search">
    <input type="search" />
    <button>搜索</button>
  </div>
</template>
```

### 区域角色

```vue
<template>
  <article role="article">
    <h1>文章标题</h1>
    <p>内容...</p>
  </article>

  <section role="region" aria-labelledby="section-title">
    <h2 id="section-title">章节标题</h2>
    <p>内容...</p>
  </section>
</template>
```

### Widget 角色

```vue
<template>
  <!-- 按钮 -->
  <div
    role="button"
    tabindex="0"
    @click="handleClick"
    @keydown.enter="handleClick"
    @keydown.space.prevent="handleClick"
  >
    自定义按钮
  </div>

  <!-- 选项卡 -->
  <div role="tablist">
    <button
      v-for="tab in tabs"
      :key="tab.id"
      role="tab"
      :aria-selected="activeTab === tab.id"
      :aria-controls="`panel-${tab.id}`"
      @click="activateTab(tab.id)"
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
  >
    {{ tab.content }}
  </div>

  <!-- 对话框 -->
  <div
    role="dialog"
    aria-modal="true"
    aria-labelledby="dialog-title"
    aria-describedby="dialog-description"
  >
    <h2 id="dialog-title">对话框标题</h2>
    <p id="dialog-description">对话框描述</p>
    <button @click="close">关闭</button>
  </div>
</template>
```

## ARIA 属性

### aria-label 和 aria-labelledby

```vue
<template>
  <!-- aria-label: 直接提供标签 -->
  <button aria-label="关闭对话框">&times;</button>

  <nav aria-label="主导航">
    <a href="/">首页</a>
    <a href="/about">关于</a>
  </nav>

  <!-- aria-labelledby: 引用页面上的元素 -->
  <section aria-labelledby="section-title">
    <h2 id="section-title">章节标题</h2>
    <p>内容...</p>
  </section>

  <!-- 多个标签 -->
  <figure aria-labelledby="figure-caption1 figure-caption2">
    <img src="/chart.jpg" alt="销售趋势图" />
    <figcaption id="figure-caption1">图1: 销售数据</figcaption>
    <p id="figure-caption2">2024年第一季度</p>
  </figure>
</template>
```

### aria-describedby

```vue
<template>
  <!-- 提供额外描述 -->
  <div>
    <label for="password">密码:</label>
    <input
      id="password"
      type="password"
      aria-describedby="password-hint"
    />
    <p id="password-hint">至少8个字符,包含字母和数字</p>
  </div>

  <!-- 错误提示 -->
  <div>
    <label for="email">邮箱:</label>
    <input
      id="email"
      v-model="email"
      aria-invalid="true"
      aria-describedby="email-error"
    />
    <span id="email-error" role="alert">请输入有效的邮箱地址</span>
  </div>
</template>
```

### aria-hidden

```vue
<template>
  <!-- 对辅助技术隐藏 -->
  <span aria-hidden="true">装饰性图标</span>

  <!-- 重复内容 -->
  <header>
    <h1>网站标题</h1>
    <nav aria-label="主导航">
      <!-- 面包屑 -->
      <nav aria-label="Breadcrumb" aria-hidden="true">
        首页 / 关于 / 团队
      </nav>
    </nav>
  </header>

  <!-- 注意: 不要对可聚焦元素使用 aria-hidden -->
  <!-- ❌ 错误 -->
  <button aria-hidden="true">点击</button>

  <!-- ✅ 正确: 使用 CSS 隐藏视觉但保持可访问 -->
  <button class="visually-hidden">关闭</button>
</template>

<style>
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
</style>
```

### aria-expanded 和 aria-controls

```vue
<template>
  <!-- 展开/收起 -->
  <button
    @click="toggle"
    :aria-expanded="isExpanded"
    :aria-controls="`panel-${id}`"
  >
    {{ isExpanded ? '收起' : '展开' }}
  </button>

  <div
    :id="`panel-${id}`"
    v-if="isExpanded"
    role="region"
    :aria-labelledby="`button-${id}`"
  >
    <p>可展开的内容...</p>
  </div>

  <!-- 下拉菜单 -->
  <div>
    <button
      @click="toggleMenu"
      :aria-expanded="isMenuOpen"
      aria-haspopup="true"
      aria-controls="menu-dropdown"
    >
      菜单
    </button>

    <ul
      v-if="isMenuOpen"
      id="menu-dropdown"
      role="menu"
    >
      <li role="none">
        <button role="menuitem">选项 1</button>
      </li>
      <li role="none">
        <button role="menuitem">选项 2</button>
      </li>
    </ul>
  </div>
</template>
```

### aria-current

```vue
<template>
  <!-- 标识当前页面/位置 -->
  <nav aria-label="面包屑">
    <ol>
      <li><a href="/">首页</a></li>
      <li>
        <a href="/products">产品</a>
        <ol>
          <li><a href="/products/electronics">电子产品</a></li>
          <li>
            <span aria-current="page">智能手机</span>
          </li>
        </ol>
      </li>
    </ol>
  </nav>

  <!-- 导航中的当前项 -->
  <nav aria-label="主导航">
    <a href="/" aria-current="false">首页</a>
    <a href="/products" aria-current="page">产品</a>
    <a href="/about" aria-current="false">关于</a>
  </nav>

  <!-- 步骤指示器 -->
  <ol>
    <li aria-current="step">步骤 1 (当前)</li>
    <li>步骤 2</li>
    <li>步骤 3</li>
  </ol>
</template>
```

### aria-live

```vue
<template>
  <!-- 实时区域更新 -->
  <div
    aria-live="polite"
    aria-atomic="true"
    role="status"
  >
    {{ message }}
  </div>

  <!-- 重要消息 -->
  <div
    role="alert"
    aria-live="assertive"
    aria-atomic="true"
  >
    操作成功!
  </div>

  <!-- 加载状态 -->
  <div
    v-if="loading"
    role="status"
    aria-live="polite"
    aria-busy="true"
  >
    正在加载...
  </div>
</template>

<script setup lang="ts">
// aria-live 级别:
// - off: 不宣布更新(默认)
// - polite: 空闲时宣布
// - assertive: 立即打断并宣布

const showMessage = () => {
  message.value = '操作已完成'
  // 屏幕阅读器会自动朗读
}
</script>
```

### aria-pressed 和 aria-checked

```vue
<template>
  <!-- 切换按钮 -->
  <button
    @click="toggle"
    :aria-pressed="isPressed"
  >
    {{ isPressed ? '已激活' : '未激活' }}
  </button>

  <!-- 复选框样式按钮 -->
  <button
    role="checkbox"
    :aria-checked="isChecked"
    @click="toggle"
  >
    同意条款
  </button>

  <!-- 单选按钮组 -->
  <div role="radiogroup" aria-labelledby="rating-label">
    <span id="rating-label">评分:</span>

    <label>
      <input
        type="radio"
        name="rating"
        value="5"
        :aria-checked="rating === '5'"
      />
      5 星
    </label>

    <label>
      <input
        type="radio"
        name="rating"
        value="4"
        :aria-checked="rating === '4'"
      />
      4 星
    </label>
  </div>
</template>
```

### aria-invalid

```vue
<template>
  <!-- 表单验证 -->
  <form @submit.prevent="submit">
    <div>
      <label for="email">邮箱:</label>
      <input
        id="email"
        v-model="email"
        type="email"
        :aria-invalid="errors.email ? 'true' : 'false'"
        :aria-describedby="errors.email ? 'email-error' : 'email-hint'"
      />
      <span id="email-hint">请输入您的邮箱</span>
      <span v-if="errors.email" id="email-error" role="alert">
        {{ errors.email }}
      </span>
    </div>

    <button type="submit">提交</button>
  </form>
</template>
```

### aria-orientation

```vue
<template>
  <!-- 分隔符 -->
  <div
    role="separator"
    aria-orientation="horizontal"
  >
    <!-- 水平线 -->
  </div>

  <div
    role="separator"
    aria-orientation="vertical"
  >
    <!-- 垂直线 -->
  </div>

  <!-- 滑块 -->
  <input
    type="range"
    aria-orientation="horizontal"
    min="0"
    max="100"
  />

  <!-- 垂直滑块 -->
  <input
    type="range"
    aria-orientation="vertical"
    style="appearance: slider-vertical"
  />
</template>
```

## 完整组件示例

### 选项卡组件

```vue
<script setup lang="ts>
import { ref } from 'vue'

const activeTab = ref('tab1')

const tabs = [
  { id: 'tab1', label: '首页', content: '首页内容' },
  { id: 'tab2', label: '产品', content: '产品信息' },
  { id: 'tab3', label: '关于', content: '关于我们' },
]

const activateTab = (tabId: string) => {
  activeTab.value = tabId
}
</script>

<template>
  <div class="tabs">
    <!-- 选项卡列表 -->
    <div role="tablist" aria-label="产品分类">
      <button
        v-for="tab in tabs"
        :key="tab.id"
        :id="`tab-${tab.id}`"
        role="tab"
        :aria-selected="activeTab === tab.id"
        :aria-controls="`panel-${tab.id}`"
        :tabindex="activeTab === tab.id ? 0 : -1"
        @click="activateTab(tab.id)"
      >
        {{ tab.label }}
      </button>
    </div>

    <!-- 选项卡面板 -->
    <div
      v-for="tab in tabs"
      :key="tab.id"
      :id="`panel-${tab.id}`"
      role="tabpanel"
      :aria-labelledby="`tab-${tab.id}`"
      :hidden="activeTab !== tab.id"
    >
      {{ tab.content }}
    </div>
  </div>
</template>
```

### 模态框组件

```vue
<script setup lang="ts">
import { ref, watch, nextTick } from 'vue'

const isOpen = ref(false)
const modalRef = ref<HTMLElement>()
const previousActiveElement = ref<HTMLElement>()

const openModal = () => {
  isOpen.value = true
}

const closeModal = () => {
  isOpen.value = false
}

watch(isOpen, async (newValue) => {
  if (newValue) {
    // 保存当前焦点元素
    previousActiveElement.value = document.activeElement as HTMLElement

    // 设置焦点到模态框
    await nextTick()
    modalRef.value?.focus()
  } else {
    // 恢复焦点
    previousActiveElement.value?.focus()
  }
})

// 捕获焦点在模态框内
const trapFocus = (event: KeyboardEvent) => {
  if (event.key === 'Tab') {
    const focusableElements = modalRef.value?.querySelectorAll(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    )
    const firstElement = focusableElements?.[0] as HTMLElement
    const lastElement = focusableElements?.[
      focusableElements.length - 1
    ] as HTMLElement

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
  <Teleport to="body">
    <div
      v-if="isOpen"
      class="modal-overlay"
      @click="closeModal"
    >
      <div
        ref="modalRef"
        class="modal"
        role="dialog"
        aria-modal="true"
        aria-labelledby="modal-title"
        aria-describedby="modal-description"
        tabindex="-1"
        @click.stop
        @keydown="trapFocus"
      >
        <header>
          <h2 id="modal-title">模态框标题</h2>
          <button
            @click="closeModal"
            aria-label="关闭对话框"
          >
            &times;
          </button>
        </header>

        <div id="modal-description">
          <p>模态框内容...</p>
        </div>

        <footer>
          <button @click="closeModal">取消</button>
          <button>确认</button>
        </footer>
      </div>
    </div>
  </Teleport>
</template>
```

## 最佳实践

1. **原生优先** - 如果 HTML 元素能实现,就不要用 ARIA
2. **不要覆盖语义** - 不要给已有语义的元素添加冲突的角色
3. **测试真实设备** - 使用屏幕阅读器验证 ARIA 实现
4. **保持同步** - 确保 ARIA 状态与实际 UI 状态同步
5. **提供标签** - 所有交互元素都需要可访问的名称

## 参考资料

- [WAI-ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/)
- [ARIA in HTML](https://www.w3.org/TR/html-aria/)
- [MDN ARIA Documentation](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA)
