---
title: 使用黑盒方法测试组件 - 关注行为而非实现
impact: HIGH
impactDescription: 感知实现的测试在重构期间变得脆弱并中断,导致高维护负担
type: best-practice
tags: [vue3, testing, component-testing, vitest, vue-test-utils, blackbox]
---

# 使用黑盒方法测试组件 - 关注行为而非实现

**影响: HIGH** - 依赖实现细节(内部状态、私有方法、组件结构)的测试在功能仍然正确时会在重构期间中断。这导致假阴性和高测试维护负担。

遵循 Kent C. Dodds 的测试哲学:"你的测试越像软件的使用方式,它们能给你的信心就越多。"

## 任务清单

- [ ] 测试组件做什么,而不是如何做
- [ ] 通过用户可见的属性(文本、角色、testid)查询元素
- [ ] 模拟用户交互(点击、输入)而不是直接调用方法
- [ ] 断言渲染输出、发出的事件和可见状态更改
- [ ] 避免访问组件内部状态或私有方法
- [ ] 对没有语义含义的元素使用 data-testid 属性

**错误:**
```javascript
import { mount } from '@vue/test-utils'
import Counter from './Counter.vue'

// 错误: 测试实现细节
test('计数器递增', async () => {
  const wrapper = mount(Counter)

  // 直接访问内部状态
  expect(wrapper.vm.count).toBe(0)

  // 调用内部方法而不是模拟用户操作
  wrapper.vm.increment()

  // 检查内部状态而不是可见输出
  expect(wrapper.vm.count).toBe(1)
})

// 错误: 测试组件结构
test('具有递增按钮', () => {
  const wrapper = mount(Counter)

  // 测试实现细节 - 如果按钮变成锚点怎么办?
  expect(wrapper.find('button').exists()).toBe(true)
})
```

**正确:**
```javascript
import { mount } from '@vue/test-utils'
import Counter from './Counter.vue'

// 正确: 像用户一样测试行为
test('点击递增后计数器显示更新值', async () => {
  const wrapper = mount(Counter, {
    props: { max: 10 }
  })

  // 断言初始可见状态
  expect(wrapper.find('[data-testid="counter-value"]').text()).toContain('0')

  // 模拟用户操作
  await wrapper.find('[data-testid="increment-button"]').trigger('click')

  // 断言可见结果
  expect(wrapper.find('[data-testid="counter-value"]').text()).toContain('1')
})

// 正确: 测试发出的事件(公共 API)
test('递增时发出带有新值的更改事件', async () => {
  const wrapper = mount(Counter)

  await wrapper.find('[data-testid="increment-button"]').trigger('click')

  expect(wrapper.emitted('change')).toHaveLength(1)
  expect(wrapper.emitted('change')[0]).toEqual([1])
})
```

## 使用 @testing-library/vue 进行更好的黑盒测试

```javascript
import { render, screen, fireEvent } from '@testing-library/vue'
import Counter from './Counter.vue'

// Testing Library 鼓励可访问的、以用户为中心的查询
test('按钮点击时递增计数器', async () => {
  render(Counter)

  // 通过角色查询 - 屏幕阅读器如何看到它
  const button = screen.getByRole('button', { name: /increment/i })
  const display = screen.getByText('0')

  await fireEvent.click(button)

  expect(screen.getByText('1')).toBeInTheDocument()
})
```

## 测试什么 vs 不测试什么

### 要测试(公共接口)
```javascript
// Props 影响渲染输出
test('显示来自 props 的标题', () => {
  const wrapper = mount(Card, {
    props: { title: 'Hello World' }
  })
  expect(wrapper.text()).toContain('Hello World')
})

// Slots 正确渲染
test('渲染 slot 内容', () => {
  const wrapper = mount(Card, {
    slots: { default: '<p>Slot content</p>' }
  })
  expect(wrapper.text()).toContain('Slot content')
})

// 发出的事件
test('点击 X 时发出关闭事件', async () => {
  const wrapper = mount(Modal)
  await wrapper.find('[data-testid="close-button"]').trigger('click')
  expect(wrapper.emitted('close')).toBeTruthy()
})
```

### 不要测试(实现细节)
```javascript
// 不要测试内部计算属性
// 不要测试内部方法
// 不要测试组件选项/setup 内部
// 不要测试渲染了特定的子组件(除非关键)
// 不要完全依赖快照测试来验证正确性
```

## 参考
- [Vue.js 测试指南](https://vuejs.org/guide/scaling-up/testing)
- [Vue Test Utils - 测试哲学](https://test-utils.vuejs.org/guide/)
- [Testing Library 指导原则](https://testing-library.com/docs/guiding-principles)
