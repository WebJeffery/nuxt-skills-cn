---
name: vue-testing-best-practices
version: 1.0.0
license: MIT
---

Vue.js 测试最佳实践、模式和常见陷阱。

### 测试
- 为 Vue 3 项目设置测试基础设施 → 见 [testing-vitest-recommended-for-vue](reference/testing-vitest-recommended-for-vue.md)
- 重构组件内部时测试不断失败 → 见 [testing-component-blackbox-approach](reference/testing-component-blackbox-approach.md)
- 测试因竞态条件间歇性失败 → 见 [testing-async-await-flushpromises](reference/testing-async-await-flushpromises.md)
- 使用生命周期钩子或 inject 的 composables 测试失败 → 见 [testing-composables-helper-wrapper](reference/testing-composables-helper-wrapper.md)
- 在测试中出现 "injection Symbol(pinia) not found" 错误 → 见 [testing-pinia-store-setup](reference/testing-pinia-store-setup.md)
- 带有异步 setup 的组件在测试中无法渲染 → 见 [testing-suspense-async-components](reference/testing-suspense-async-components.md)
- 尽管功能损坏,快照测试仍然通过 → 见 [testing-no-snapshot-only](reference/testing-no-snapshot-only.md)
- 为 Vue 应用选择端到端测试框架 → 见 [testing-e2e-playwright-recommended](reference/testing-e2e-playwright-recommended.md)
- 测试需要验证计算样式或真实 DOM 事件 → 见 [testing-browser-vs-node-runners](reference/testing-browser-vs-node-runners.md)
- 测试使用 defineAsyncComponent 创建的组件失败 → 见 [async-component-testing](reference/async-component-testing.md)
- 在包装器查询中找不到传送的模态框内容 → 见 [teleport-testing-complexity](reference/teleport-testing-complexity.md)

## 参考
- [Vue.js 测试指南](https://vuejs.org/guide/scaling-up/testing)
- [Vue Test Utils](https://test-utils.vuejs.org/)
- [Vitest 文档](https://vitest.dev/)
- [Playwright 文档](https://playwright.dev/)
