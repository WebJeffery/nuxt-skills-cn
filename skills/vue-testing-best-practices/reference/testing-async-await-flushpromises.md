---
title: 使用 nextTick 和 flushPromises 正确处理异步更新
impact: HIGH
impactDescription: 竞态条件和脆弱的测试发生在异步 DOM 更新或 API 调用在断言运行后完成时
type: gotcha
tags: [vue3, testing, async, flushPromises, nextTick, vitest, vue-test-utils, race-condition]
---

# 使用 nextTick 和 flushPromises 正确处理异步更新

**影响: HIGH** - Vue 异步更新 DOM。如果不正确等待这些更新,测试可能会对过时的 DOM 状态进行断言,导致间歇性失败和假阴性。

对触发和 setValue 使用 `await`,对响应式更新使用 `nextTick`,对外部异步操作(如 API 调用)使用 `flushPromises`。

## 任务清单

- [ ] 始终 await `trigger()` 和 `setValue()` 调用
- [ ] 在编程式响应式状态更改后使用 `await nextTick()`
- [ ] 对外部异步操作(API 调用、计时器)使用 `await flushPromises()`
- [ ] 不要链接多个 `nextTick` 调用 - 使用 `flushPromises` 代替
- [ ] 考虑使用 testing-library 的 `waitFor` 进行轮询断言

**错误:**
```javascript
import { mount } from '@vue/test-utils'
import SearchComponent from './SearchComponent.vue'

// 错误: 不等待 trigger - 断言在 DOM 更新前运行
test('搜索过滤结果', () => {
  const wrapper = mount(SearchComponent)

  wrapper.find('input').setValue('vue')  // 缺少 await!
  wrapper.find('button').trigger('click')  // 缺少 await!

  // 此断言可能失败 - DOM 尚未更新
  expect(wrapper.findAll('.result').length).toBe(3)
})

// 错误: 对 API 调用使用 nextTick
test('从 API 加载数据', async () => {
  const wrapper = mount(DataLoader)

  await nextTick()  // 这不会等待 API 调用!

  // 断言在 fetch 完成前运行
  expect(wrapper.find('.data').text()).toBe('Loaded data')
})
```

**正确:**
```javascript
import { mount, flushPromises } from '@vue/test-utils'
import { nextTick } from 'vue'
import SearchComponent from './SearchComponent.vue'
import DataLoader from './DataLoader.vue'

// 正确: Await trigger 和 setValue
test('搜索过滤结果', async () => {
  const wrapper = mount(SearchComponent)

  await wrapper.find('input').setValue('vue')
  await wrapper.find('button').trigger('click')

  expect(wrapper.findAll('.result').length).toBe(3)
})

// 正确: 对 API 调用使用 flushPromises
test('从 API 加载数据', async () => {
  const wrapper = mount(DataLoader)

  // 等待所有挂起的 Promise 解析
  await flushPromises()

  expect(wrapper.find('.data').text()).toBe('Loaded data')
})
```

## 何时使用每种方法

### `await trigger()` / `await setValue()` - 用户交互
```javascript
// 这些方法内部返回 nextTick
await wrapper.find('button').trigger('click')
await wrapper.find('input').setValue('new value')
await wrapper.find('form').trigger('submit')
```

### `await nextTick()` - 编程式响应式更新
```javascript
import { nextTick } from 'vue'

test('反映编程式状态更改', async () => {
  const wrapper = mount(Counter)

  // 直接状态修改(当测试暴露的内部时)
  wrapper.vm.count = 5

  await nextTick()  // 等待 Vue 更新 DOM

  expect(wrapper.find('.count').text()).toBe('5')
})
```

### `await flushPromises()` - 外部异步操作
```javascript
import { flushPromises } from '@vue/test-utils'

test('显示获取的数据', async () => {
  const wrapper = mount(UserProfile, {
    props: { userId: 1 }
  })

  // 等待组件的 API 调用完成
  await flushPromises()

  expect(wrapper.find('.username').text()).toBe('John')
})

// 有时你需要多个 flushPromises 进行链式异步操作
test('在 fetch 后处理数据', async () => {
  const wrapper = mount(DataProcessor)

  await flushPromises()  // 等待 fetch
  await flushPromises()  // 等待由 fetch 触发的处理

  expect(wrapper.find('.processed').exists()).toBe(true)
})
```

## 常见模式: 组合方法
```javascript
test('提交表单并显示成功', async () => {
  const wrapper = mount(ContactForm)

  // 填写表单(awaiting 每个交互)
  await wrapper.find('#name').setValue('John')
  await wrapper.find('#email').setValue('john@example.com')

  // 提交表单
  await wrapper.find('form').trigger('submit')

  // 等待 API 提交完成
  await flushPromises()

  // 断言成功状态
  expect(wrapper.find('.success-message').exists()).toBe(true)
})
```

## 使用 MSW 或模拟 API 测试
```javascript
import { flushPromises } from '@vue/test-utils'
import { rest } from 'msw'
import { setupServer } from 'msw/node'

const server = setupServer(
  rest.get('/api/user', (req, res, ctx) => {
    return res(ctx.json({ name: 'John' }))
  })
)

test('显示用户数据', async () => {
  const wrapper = mount(UserCard)

  // MSW 可能需要多个 flushPromises
  await flushPromises()
  await flushPromises()

  expect(wrapper.find('.name').text()).toBe('John')
})
```

## 参考
- [Vue Test Utils - 异步行为](https://test-utils.vuejs.org/guide/advanced/async-suspense)
- [Vue.js 测试指南](https://vuejs.org/guide/scaling-up/testing)
