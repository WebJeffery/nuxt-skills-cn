---
title: 避免仅依赖快照测试 - 它们难以维护且脆弱
impact: MEDIUM
impactDescription: 快照测试难以维护,容易产生误报,并且无法捕获逻辑错误
type: best-practice
tags: [vue3, testing, vitest, vue-test-utils, snapshot, maintenance]
---

# 避免仅依赖快照测试 - 它们难以维护且脆弱

**影响: MEDIUM** - 快照测试提供低价值,高维护成本。它们容易产生误报(对无关更改失败),无法捕获逻辑错误,并且不传达测试意图。

使用快照测试作为回归检查,但主要测试应断言特定的、有意义的行为和输出。

## 任务清单

- [ ] 避免创建仅包含快照的测试
- [ ] 对快照测试添加有意义的断言
- [ ] 使用快照测试验证复杂 UI 结构或视觉回归
- [ ] 对快照使用内联快照以提高可读性
- [ ] 使用描述性快照名称
- [ ] 定期审查和更新快照

**错误:**
```javascript
import { mount } from '@vue/test-utils'
import UserProfile from './UserProfile.vue'

// 错误: 仅快照 - 不传达意图
test('渲染用户资料', () => {
  const wrapper = mount(UserProfile, {
    props: {
      user: {
        name: 'John Doe',
        email: 'john@example.com',
        avatar: 'avatar.jpg'
      }
    }
  })

  expect(wrapper.html()).toMatchSnapshot()
})

// 问题:
// 1. 不传达测试意图
// 2. 任何 UI 更改都会导致失败(误报)
// 3. 不会捕获逻辑错误
// 4. 难以审查快照以验证正确性
```

**正确:**
```javascript
import { mount } from '@vue/test-utils'
import UserProfile from './UserProfile.vue'

// 正确: 有意义的断言 + 快照作为回归检查
test('渲染用户资料', () => {
  const wrapper = mount(UserProfile, {
    props: {
      user: {
        name: 'John Doe',
        email: 'john@example.com',
        avatar: 'avatar.jpg'
      }
    }
  })

  // 有意义的断言 - 传达意图
  expect(wrapper.find('[data-testid="user-name"]').text()).toBe('John Doe')
  expect(wrapper.find('[data-testid="user-email"]').text()).toBe('john@example.com')
  expect(wrapper.find('[data-testid="user-avatar"]').attributes('src')).toBe('avatar.jpg')

  // 快照作为回归检查 - 捕获意外结构更改
  expect(wrapper.html()).toMatchSnapshot()
})
```

## 内联快照以提高可读性

```javascript
test('渲染用户资料', () => {
  const wrapper = mount(UserProfile, {
    props: {
      user: {
        name: 'John Doe',
        email: 'john@example.com',
        avatar: 'avatar.jpg'
      }
    }
  })

  // 内联快照 - 在测试文件中可见
  expect(wrapper.html()).toMatchInlineSnapshot(`
    <div class="user-profile">
      <img src="avatar.jpg" alt="John Doe" data-testid="user-avatar" />
      <h2 data-testid="user-name">John Doe</h2>
      <p data-testid="user-email">john@example.com</p>
    </div>
  `)
})
```

## 快照测试的适当用例

### 1. 复杂 UI 结构验证
```javascript
test('渲染复杂的表格结构', () => {
  const wrapper = mount(DataTable, {
    props: {
      columns: ['Name', 'Age', 'Email'],
      data: [
        { name: 'John', age: 30, email: 'john@example.com' },
        { name: 'Jane', age: 25, email: 'jane@example.com' }
      ]
    }
  })

  // 快照捕获整个表格结构
  expect(wrapper.html()).toMatchSnapshot('data-table-structure')
})
```

### 2. 视觉回归检查
```javascript
test('错误消息样式保持一致', () => {
  const wrapper = mount(ErrorMessage, {
    props: { message: 'Something went wrong' }
  })

  // 快照确保错误消息样式保持一致
  expect(wrapper.html()).toMatchSnapshot('error-message-styles')
})
```

### 3. 集成测试
```javascript
test('完整的注册表单渲染', () => {
  const wrapper = mount(RegistrationForm)

  // 快照捕获整个表单结构
  expect(wrapper.html()).toMatchSnapshot('registration-form')
})
```

## 快照测试的常见陷阱

### 1. 过度使用快照
```javascript
// 错误: 对简单组件使用快照
test('按钮渲染', () => {
  const wrapper = mount(Button, { props: { label: 'Click me' } })
  expect(wrapper.html()).toMatchSnapshot()  // 过度使用
})

// 正确: 对简单组件使用断言
test('按钮渲染', () => {
  const wrapper = mount(Button, { props: { label: 'Click me' } })
  expect(wrapper.text()).toBe('Click me')
  expect(wrapper.classes()).toContain('btn')
})
```

### 2. 忽略快照更新
```javascript
// 错误: 不审查快照更新
// 当快照失败时,盲目运行 --update 而不审查更改

// 正确: 审查快照更新
// 1. 检查快照失败
// 2. 验证更改是否预期
// 3. 如果是,更新快照
// 4. 如果否,修复代码
```

### 3. 快照包含动态内容
```javascript
// 错误: 快照包含动态内容(时间戳、随机 ID)
test('渲染用户列表', () => {
  const wrapper = mount(UserList, {
    props: { users: [{ id: Date.now(), name: 'John' }] }
  })
  expect(wrapper.html()).toMatchSnapshot()  // 每次都会失败
})

// 正确: 模拟或固定动态内容
test('渲染用户列表', () => {
  vi.useFakeTimers()
  vi.setSystemTime('2024-01-01')

  const wrapper = mount(UserList, {
    props: { users: [{ id: 1, name: 'John' }] }
  })
  expect(wrapper.html()).toMatchSnapshot()

  vi.useRealTimers()
})
```

## 快照测试最佳实践

```javascript
// 1. 使用描述性快照名称
expect(wrapper.html()).toMatchSnapshot('user-profile-with-avatar')

// 2. 对不同场景使用多个快照
test('不同按钮变体', () => {
  expect(mount(Button, { props: { variant: 'primary' } }).html())
    .toMatchSnapshot('button-primary')

  expect(mount(Button, { props: { variant: 'secondary' } }).html())
    .toMatchSnapshot('button-secondary')
})

// 3. 将快照与有意义的断言结合
test('模态框渲染', () => {
  const wrapper = mount(Modal, {
    props: { open: true, title: 'Confirm' }
  })

  // 有意义的断言
  expect(wrapper.find('[data-testid="modal"]').classes()).toContain('open')
  expect(wrapper.find('[data-testid="modal-title"]').text()).toBe('Confirm')

  // 快照作为回归检查
  expect(wrapper.html()).toMatchSnapshot('modal-open')
})

// 4. 使用属性快照进行特定检查
test('按钮属性', () => {
  const wrapper = mount(Button, {
    props: { disabled: true, type: 'submit' }
  })

  expect(wrapper.attributes()).toMatchInlineSnapshot({
    disabled: '',
    type: 'submit'
  })
})
```

## 参考
- [Vue.js 测试指南 - 快照测试](https://vuejs.org/guide/scaling-up/testing#snapshot-testing)
- [Jest 快照测试最佳实践](https://jestjs.io/docs/snapshot-testing)
