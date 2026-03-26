---
title: 传送的内容需要特殊的测试方法
impact: MEDIUM
impactDescription: Vue Test Utils 无法使用标准 wrapper.find() 方法找到传送的内容
type: gotcha
tags: [vue3, teleport, testing, vue-test-utils]
---

# 传送的内容需要特殊的测试方法

**影响: MEDIUM** - Vue Test Utils 将查询范围限制在挂载的组件内。传送的内容在组件的 DOM 树之外渲染,因此 `wrapper.find()` 无法定位它。这导致测试失败和困惑。

## 任务清单

- [ ] 在单元测试中存根 Teleport 以将内容保留在组件树中
- [ ] 使用 `document.body` 查询进行带有真实 Teleport 的集成测试
- [ ] 考虑对传送的组件使用 `getComponent()` 而不是 DOM 查询

**问题 - 标准测试失败:**
```vue
<!-- Modal.vue -->
<template>
  <button @click="open = true">Open</button>
  <Teleport to="body">
    <div v-if="open" class="modal" data-testid="modal">
      <input type="text" data-testid="modal-input" />
    </div>
  </Teleport>
</template>
```

```ts
// Modal.spec.ts - 损坏
import { mount } from '@vue/test-utils'
import Modal from './Modal.vue'

test('模态框输入存在', async () => {
  const wrapper = mount(Modal)
  await wrapper.find('button').trigger('click')

  // 失败: 传送的内容不在包装器的 DOM 树中
  expect(wrapper.find('[data-testid="modal-input"]').exists()).toBe(true)
})
```

**解决方案 1 - 存根 Teleport:**
```ts
import { mount } from '@vue/test-utils'
import Modal from './Modal.vue'

test('模态框输入存在', async () => {
  const wrapper = mount(Modal, {
    global: {
      stubs: {
        // 存根 teleport 以内联渲染内容
        Teleport: true
      }
    }
  })

  await wrapper.find('button').trigger('click')

  // 有效: 内容在包装器内渲染
  expect(wrapper.find('[data-testid="modal-input"]').exists()).toBe(true)
})
```

**解决方案 2 - 查询文档主体:**
```ts
import { mount } from '@vue/test-utils'
import Modal from './Modal.vue'

test('模态框渲染到主体', async () => {
  const wrapper = mount(Modal, {
    attachTo: document.body  // Teleport 工作所必需
  })

  await wrapper.find('button').trigger('click')

  // 查询实际 DOM
  const modal = document.querySelector('[data-testid="modal"]')
  expect(modal).toBeTruthy()

  const input = document.querySelector('[data-testid="modal-input"]')
  expect(input).toBeTruthy()

  // 清理
  wrapper.unmount()
})
```

**解决方案 3 - 带内容访问的自定义 Teleport 存根:**
```ts
import { mount, config } from '@vue/test-utils'
import { h, Teleport } from 'vue'
import Modal from './Modal.vue'

// 自定义存根,以可测试的方式渲染内容
const TeleportStub = {
  setup(props, { slots }) {
    return () => h('div', { class: 'teleport-stub' }, slots.default?.())
  }
}

test('带自定义存根的模态框', async () => {
  const wrapper = mount(Modal, {
    global: {
      stubs: {
        Teleport: TeleportStub
      }
    }
  })

  await wrapper.find('button').trigger('click')

  // 内容在 .teleport-stub 内
  expect(wrapper.find('.teleport-stub [data-testid="modal-input"]').exists()).toBe(true)
})
```

## 测试 Vue Final Modal 和 UI 库

像 Vue Final Modal 这样的库在内部使用 Teleport,导致测试失败:

```ts
// 问题: Vue Final Modal 传送到主体
import { VueFinalModal } from 'vue-final-modal'

test('模态框内容', async () => {
  const wrapper = mount(MyComponent, {
    global: {
      stubs: {
        // 存根模态框组件以避免 teleport 问题
        VueFinalModal: true
      }
    }
  })
})
```

## E2E 测试(Cypress, Playwright)

E2E 测试查询真实 DOM,因此 Teleport 自然工作:

```ts
// Cypress
it('打开模态框', () => {
  cy.visit('/page-with-modal')
  cy.get('button').click()

  // 有效: Cypress 查询真实 DOM
  cy.get('[data-testid="modal"]').should('be.visible')
})
```

## 参考
- [Vue Test Utils - Teleport](https://test-utils.vuejs.org/guide/advanced/teleport)
- [Vue Test Utils - Stubs](https://test-utils.vuejs.org/guide/advanced/stubs-shallow-mount)
