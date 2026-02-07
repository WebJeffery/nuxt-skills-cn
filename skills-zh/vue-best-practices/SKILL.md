---
name: vue-best-practices
description: 必须用于 Vue.js 任务。强烈推荐使用 Composition API 配合 `<script setup>` 和 TypeScript 作为标准方法。涵盖 Vue 3、SSR、Volar、vue-tsc。对于任何 Vue、.vue 文件、Vue Router、Pinia 或使用 Vue 的 Vite 工作都需要加载。除非项目明确要求使用 Options API，否则始终使用 Composition API。
license: MIT
metadata:
  author: github.com/vuejs-ai
  version: "17.0.0"
---

Vue 3 最佳实践、常见陷阱和性能优化。

### 响应式
- 在脚本中访问 ref() 值时未使用 .value → 请参阅 [ref-value-access](reference/ref-value-access.md)
- 解构 reactive() 对象时丢失响应性 → 请参阅 [reactive-destructuring](reference/reactive-destructuring.md)
- 在状态中选择使用 ref() 还是 reactive() → 请参阅 [prefer-ref-over-reactive](reference/prefer-ref-over-reactive.md)
- 在数组和集合中访问 refs → 请参阅 [refs-in-collections-need-value](reference/refs-in-collections-need-value.md)
- 大型对象或外部库数据的开销 → 请参阅 [shallow-ref-for-performance](reference/shallow-ref-for-performance.md)
- 在模板表达式中使用嵌套 refs → 请参阅 [template-ref-unwrapping-top-level](reference/template-ref-unwrapping-top-level.md)
- 使用 === 运算符比较响应式对象 → 请参阅 [reactivity-proxy-identity-hazard](reference/reactivity-proxy-identity-hazard.md)
- 库实例在响应式状态中失效 → 请参阅 [reactivity-markraw-for-non-reactive](reference/reactivity-markraw-for-non-reactive.md)
- 期望每次状态更改都触发监听器 → 请参阅 [reactivity-same-tick-batching](reference/reactivity-same-tick-batching.md)
- 集成外部状态管理库 → 请参阅 [reactivity-external-state-integration](reference/reactivity-external-state-integration.md)
- 使用 watchEffect 而不是 computed 来派生状态 → 请参阅 [reactivity-computed-over-watcheffect-mutations](reference/reactivity-computed-over-watcheffect-mutations.md)

### 计算属性
- 计算属性 getter 正在进行 API 调用或修改 → 请参阅 [computed-no-side-effects](reference/computed-no-side-effects.md)
- 修改计算属性值导致意外丢失更改 → 请参阅 [computed-return-value-readonly](reference/computed-return-value-readonly.md)
- 计算属性未按预期更新 → 请参阅 [computed-conditional-dependencies](reference/computed-conditional-dependencies.md)
- 排序或反转数组破坏原始数据 → 请参阅 [computed-array-mutation](reference/computed-array-mutation.md)
- 昂贵的操作每次渲染时运行过于频繁 → 请参阅 [computed-vs-methods-caching](reference/computed-vs-methods-caching.md)
- 尝试向计算属性传递参数 → 请参阅 [computed-no-parameters](reference/computed-no-parameters.md)
- 复杂条件导致内联类绑定臃肿 → 请参阅 [computed-properties-for-class-logic](reference/computed-properties-for-class-logic.md)

### 监听器
- 需要监听响应式对象的属性 → 请参阅 [watch-reactive-property-getter](reference/watch-reactive-property-getter.md)
- 大型嵌套数据结构导致性能问题 → 请参阅 [watch-deep-performance](reference/watch-deep-performance.md)
- 在 await 之后访问的依赖项未被跟踪 → 请参阅 [watcheffect-async-dependency-tracking](reference/watcheffect-async-dependency-tracking.md)
- 需要在监听器中访问更新后的 DOM → 请参阅 [watch-flush-timing](reference/watch-flush-timing.md)
- 不确定使用 watch 还是 watchEffect → 请参阅 [watch-vs-watcheffect](reference/watch-vs-watcheffect.md)
- 重复初始调用和 watch 回调 → 请参阅 [watch-immediate-option](reference/watch-immediate-option.md)
- 无法正确比较旧值和新值 → 请参阅 [watch-deep-same-object-reference](reference/watch-deep-same-object-reference.md)
- 模板 refs 出现 null 或过期 → 请参阅 [watcheffect-flush-post-for-refs](reference/watcheffect-flush-post-for-refs.md)

### 组件
- 子组件正在修改 prop 值 → 请参阅 [props-are-read-only](reference/props-are-read-only.md)
- 祖父组件无法监听孙组件发出的事件 → 请参阅 [component-events-dont-bubble](reference/component-events-dont-bubble.md)
- 区分 Vue 组件和原生元素 → 请参阅 [component-naming-pascalcase](reference/component-naming-pascalcase.md)
- 递归组件需要引用自身 → 请参阅 [self-referencing-component-name](reference/self-referencing-component-name.md)
- 打包包含未使用的组件 → 请参阅 [prefer-local-component-registration](reference/prefer-local-component-registration.md)
- 通过组件 ref 访问导致紧密耦合 → 请参阅 [prefer-props-emit-over-component-refs](reference/prefer-props-emit-over-component-refs.md)

### Props 和 Emits
- Boolean prop 未按预期解析 → 请参阅 [prop-boolean-casting-order](reference/prop-boolean-casting-order.md)
- Composable 在 props 更改时不更新 → 请参阅 [prop-composable-reactivity-loss](reference/prop-composable-reactivity-loss.md)
- 解构的 props 不更新监听器 → 请参阅 [prop-destructured-watch-getter](reference/prop-destructured-watch-getter.md)
- Prop 验证需要组件实例数据 → 请参阅 [prop-validation-before-instance](reference/prop-validation-before-instance.md)
- 模板和脚本中的事件名称不一致 → 请参阅 [emit-kebab-case-in-templates](reference/emit-kebab-case-in-templates.md)
- 事件载荷在开发期间需要验证 → 请参阅 [emit-validation-for-complex-payloads](reference/emit-validation-for-complex-payloads.md)

### 模板
- 将不受信任的用户内容渲染为 HTML → 请参阅 [v-html-xss-security](reference/v-html-xss-security.md)
- 过滤或条件隐藏列表项 → 请参阅 [no-v-if-with-v-for](reference/no-v-if-with-v-for.md)
- 模板中的函数意外修改数据 → 请参阅 [template-functions-no-side-effects](reference/template-functions-no-side-effects.md)
- 过滤或排序列表时的性能问题 → 请参阅 [v-for-use-computed-for-filtering](reference/v-for-use-computed-for-filtering.md)
- 在条件渲染中选择 v-if 还是 v-show → 请参阅 [v-if-vs-v-show-performance](reference/v-if-vs-v-show-performance.md)

### 表单和 v-model
- 需要在子组件中处理 v-model 修饰符 → 请参阅 [definemodel-hidden-modifier-props](reference/definemodel-hidden-modifier-props.md)
- 需要在更改后立即使用更新的值 → 请参阅 [definemodel-value-next-tick](reference/definemodel-value-next-tick.md)
- 将 Vue 2 组件迁移到 Vue 3 → 请参阅 [v-model-vue3-breaking-changes](reference/v-model-vue3-breaking-changes.md)

### 事件和修饰符
- 需要只处理一次相同事件 → 请参阅 [event-once-modifier-for-single-use](reference/event-once-modifier-for-single-use.md)
- 键盘快捷键意外触发修饰符组合 → 请参阅 [exact-modifier-for-precise-shortcuts](reference/exact-modifier-for-precise-shortcuts.md)
- 使用左手鼠标或非标准输入设备 → 请参阅 [mouse-button-modifiers-intent](reference/mouse-button-modifiers-intent.md)
- 同时阻止默认浏览器操作和滚动性能 → 请参阅 [no-passive-with-prevent](reference/no-passive-with-prevent.md)

### 生命周期
- 生命周期钩子不异步执行 → 请参阅 [lifecycle-hooks-synchronous-registration](reference/lifecycle-hooks-synchronous-registration.md)
- 昂贵的操作严重影响性能 → 请参阅 [updated-hook-performance](reference/updated-hook-performance.md)

### 插槽
- 在插槽内容中访问子组件数据 → 请参阅 [slot-render-scope-parent-only](reference/slot-render-scope-parent-only.md)
- 混合命名插槽和作用域插槽 → 请参阅 [slot-named-scoped-explicit-default](reference/slot-named-scoped-explicit-default.md)
- 在原生 HTML 元素上使用 v-slot → 请参阅 [slot-v-slot-on-components-or-templates-only](reference/slot-v-slot-on-components-or-templates-only.md)
- 不必要地渲染空的包装元素 → 请参阅 [slot-conditional-rendering-with-slots](reference/slot-conditional-rendering-with-slots.md)
- 作用域插槽 props 缺少 TypeScript 类型安全 → 请参阅 [slot-define-slots-for-typescript](reference/slot-define-slots-for-typescript.md)
- 在没有默认值的情况下渲染空的组件插槽 → 请参阅 [slot-fallback-content-default-values](reference/slot-fallback-content-default-values.md)
- 对哪个插槽内容放在哪里感到困惑 → 请参阅 [slot-implicit-default-content](reference/slot-implicit-default-content.md)
- 期望作用域插槽 props 中有 name 属性 → 请参阅 [slot-name-reserved-prop](reference/slot-name-reserved-prop.md)
- 在无渲染组件和 composables 之间选择 → 请参阅 [slot-renderless-components-vs-composables](reference/slot-renderless-components-vs-composables.md)

### Provide/Inject
- 大型应用中的字符串键冲突 → 请参阅 [provide-inject-symbol-keys](reference/provide-inject-symbol-keys.md)
- 状态修改分散在组件中 → 请参阅 [provide-inject-mutations-in-provider](reference/provide-inject-mutations-in-provider.md)
- 通过多个组件层传递 props → 请参阅 [avoid-prop-drilling-use-provide-inject](reference/avoid-prop-drilling-use-provide-inject.md)

### Attrs
- 在 JavaScript 代码中访问连字符属性 → 请参阅 [attrs-hyphenated-property-access](reference/attrs-hyphenated-property-access.md)
- 使用 watch() 监听透传属性的更改 → 请参阅 [attrs-not-reactive](reference/attrs-not-reactive.md)

### Composables
- Composable 具有影响外部状态的意外副作用 → 请参阅 [composable-avoid-hidden-side-effects](reference/composable-avoid-hidden-side-effects.md)
- 从更小、更专注的 composables 构建复杂逻辑 → 请参阅 [composable-composition-pattern](reference/composable-composition-pattern.md)
- Composable 命名不一致或解构丢失响应性 → 请参阅 [composable-naming-return-pattern](reference/composable-naming-return-pattern.md)
- Composable 有许多可选参数或参数顺序混乱 → 请参阅 [composable-options-object-pattern](reference/composable-options-object-pattern.md)
- 需要防止 composable 状态的未控制修改 → 请参阅 [composable-readonly-state](reference/composable-readonly-state.md)
- 不确定逻辑属于 composable 还是工具函数 → 请参阅 [composable-vs-utility-functions](reference/composable-vs-utility-functions.md)

### Composition API
- 优化生产打包大小和性能 → 请参阅 [composition-api-bundle-size-minification](reference/composition-api-bundle-size-minification.md)
- Composition API 代码变得分散且难以维护 → 请参阅 [composition-api-code-organization](reference/composition-api-code-organization.md)
- 修复 mixins 中的命名冲突和不清楚的数据来源 → 请参阅 [composition-api-mixins-replacement](reference/composition-api-mixins-replacement.md)
- 将函数模式错误地应用于 Vue 状态 → 请参阅 [composition-api-not-functional-programming](reference/composition-api-not-functional-programming.md)
- 逐步迁移大型 Options API 代码库 → 请参阅 [composition-api-options-api-coexistence](reference/composition-api-options-api-coexistence.md)
- 来自 React，不必要地过度设计 Vue 模式 → 请参阅 [composition-api-vs-react-hooks-differences](reference/composition-api-vs-react-hooks-differences.md)

### 指令
- 在指令钩子之间存储状态 → 请参阅 [directive-arguments-read-only](reference/directive-arguments-read-only.md)
- 将自定义指令应用于 Vue 组件 → 请参阅 [directive-avoid-on-components](reference/directive-avoid-on-components.md)
- 在指令中创建定时器或事件监听器 → 请参阅 [directive-cleanup-in-unmounted](reference/directive-cleanup-in-unmounted.md)
- 使用相同的行为简化指令 → 请参阅 [directive-function-shorthand](reference/directive-function-shorthand.md)
- 在 script setup 中使用自定义指令 → 请参阅 [directive-naming-v-prefix](reference/directive-naming-v-prefix.md)
- 在自定义指令和内置指令之间选择 → 请参阅 [directive-prefer-declarative-templating](reference/directive-prefer-declarative-templating.md)
- 在指令和组件之间做决定 → 请参阅 [directive-vs-component-decision](reference/directive-vs-component-decision.md)
- 将 Vue 2 指令迁移到 Vue 3 → 请参阅 [directive-vue2-migration-hooks](reference/directive-vue2-migration-hooks.md)

### 过渡
- 在过渡中包装多个元素或组件 → 请参阅 [transition-single-element-slot](reference/transition-single-element-slot.md)
- 在相同元素类型之间转换但没有动画 → 请参阅 [transition-key-for-same-element](reference/transition-key-for-same-element.md)
- 使用 JavaScript 动画但未调用 done 回调 → 请参阅 [transition-js-hooks-done-callback](reference/transition-js-hooks-done-callback.md)
- 使用 TransitionGroup 为列表制作动画但没有唯一键 → 请参阅 [transition-group-key-requirement](reference/transition-group-key-requirement.md)
- 列表动画卡顿的性能问题 → 请参阅 [transition-animate-transform-opacity](reference/transition-animate-transform-opacity.md)
- 内联列表元素的移动动画失败 → 请参阅 [transition-group-flip-inline-elements](reference/transition-group-flip-inline-elements.md)
- 列表项跳转而不是平滑动画 → 请参阅 [transition-group-move-animation-position-absolute](reference/transition-group-move-animation-position-absolute.md)
- Vue 2 到 Vue 3 过渡布局意外中断 → 请参阅 [transition-group-no-default-wrapper-vue3](reference/transition-group-no-default-wrapper-vue3.md)
- 尝试使用 mode 属性对列表动画进行排序 → 请参阅 [transition-group-no-mode-prop](reference/transition-group-no-mode-prop.md)
- 为列表项动画创建级联延迟 → 请参阅 [transition-group-staggered-animations](reference/transition-group-staggered-animations.md)
- 过渡期间元素重叠或布局跳转 → 请参阅 [transition-mode-out-in](reference/transition-mode-out-in.md)
- 嵌套过渡动画过早中断 → 请参阅 [transition-nested-duration](reference/transition-nested-duration.md)
- 可重用过渡组件和作用域样式中断 → 请参阅 [transition-reusable-scoped-style](reference/transition-reusable-scoped-style.md)
- RouterView 过渡在页面加载时意外动画 → 请参阅 [transition-router-view-appear](reference/transition-router-view-appear.md)
- 混合 CSS 过渡和动画导致时序问题 → 请参阅 [transition-type-when-mixed](reference/transition-type-when-mixed.md)
- 快速过渡替换期间组件清理未触发 → 请参阅 [transition-unmount-hook-timing](reference/transition-unmount-hook-timing.md)

### 动画
- 需要为保留在 DOM 中的元素制作动画 → 请参阅 [animation-class-based-technique](reference/animation-class-based-technique.md)
- 内容更改时动画未触发 → 请参阅 [animation-key-for-rerender](reference/animation-key-for-rerender.md)
- 使用用户输入构建交互式动画 → 请参阅 [animation-state-driven-technique](reference/animation-state-driven-technique.md)
- 列表更改动画导致明显延迟 → 请参阅 [animation-transitiongroup-performance](reference/animation-transitiongroup-performance.md)

### KeepAlive
- 使用 KeepAlive 但没有适当的缓存限制或清理 → 请参阅 [keepalive-memory-management](reference/keepalive-memory-management.md)
- KeepAlive include/exclude props 不匹配缓存的组件 → 请参阅 [keepalive-component-name-requirement](reference/keepalive-component-name-requirement.md)
- 需要以编程方式从 KeepAlive 缓存中移除组件 → 请参阅 [keepalive-no-cache-removal-vue3](reference/keepalive-no-cache-removal-vue3.md)
- 用户在期望新页面数据时看到过时的缓存内容 → 请参阅 [keepalive-router-fresh-vs-cached](reference/keepalive-router-fresh-vs-cached.md)
- 使用嵌套 Vue Router 路由时子组件挂载两次 → 请参阅 [keepalive-router-nested-double-mount](reference/keepalive-router-nested-double-mount.md)
- 结合 KeepAlive 和 Transition 动画时内存增长 → 请参阅 [keepalive-transition-memory-leak](reference/keepalive-transition-memory-leak.md)
- 在动态组件之间切换时动态组件状态重置 → 请参阅 [dynamic-components-with-keepalive](reference/dynamic-components-with-keepalive.md)

### 异步组件
- 设置 Vue Router 路由组件加载 → 请参阅 [async-component-vue-router](reference/async-component-vue-router.md)
- 异步组件选项被父级 Suspense 忽略 → 请参阅 [async-component-suspense-control](reference/async-component-suspense-control.md)
- 使用 SSR 应用改善可交互时间 → 请参阅 [async-component-hydration-strategies](reference/async-component-hydration-strategies.md)
- 快速网络上的加载微调器闪烁 → 请参阅 [async-component-loading-delay](reference/async-component-loading-delay.md)

### 渲染函数
- 来自 setup 的渲染函数不响应式更新 → 请参阅 [rendering-render-function-return-from-setup](reference/rendering-render-function-return-from-setup.md)
- 同一个 vnode 在树中出现多次 → 请参阅 [render-function-vnodes-must-be-unique](reference/render-function-vnodes-must-be-unique.md)
- 在渲染函数中渲染列表没有键 → 请参阅 [render-function-v-for-keys-required](reference/render-function-v-for-keys-required.md)
- 在渲染函数中实现 .stop、.prevent → 请参阅 [render-function-event-modifiers](reference/render-function-event-modifiers.md)
- 在渲染函数中实现组件的双向绑定 → 请参阅 [render-function-v-model-implementation](reference/render-function-v-model-implementation.md)
- 在渲染函数中使用字符串名称作为组件 → 请参阅 [rendering-resolve-component-for-string-names](reference/rendering-resolve-component-for-string-names.md)
- 访问 vnode 内部属性如 el 或 shapeFlag → 请参阅 [render-function-avoid-internal-vnode-properties](reference/render-function-avoid-internal-vnode-properties.md)
- 创建简单的无状态展示组件 → 请参阅 [render-function-functional-components](reference/render-function-functional-components.md)
- 在渲染函数中应用自定义指令 → 请参阅 [render-function-custom-directives](reference/render-function-custom-directives.md)
- 监听器或深度监听器导致过度重新渲染 → 请参阅 [rendering-excessive-rerenders-watch-vs-computed](reference/rendering-excessive-rerenders-watch-vs-computed.md)
- 选择渲染函数而不是模板 → 请参阅 [rendering-prefer-templates-over-render-functions](reference/rendering-prefer-templates-over-render-functions.md)
- 将 Vue 2 渲染函数迁移到 Vue 3 → 请参阅 [rendering-render-function-h-import-vue3](reference/rendering-render-function-h-import-vue3.md)
- 错误地将插槽内容传递给 h() → 请参阅 [rendering-render-function-slots-as-functions](reference/rendering-render-function-slots-as-functions.md)
- 理解 Vue 的 vdom 优化块 → 请参阅 [rendering-understand-vdom-block-structure](reference/rendering-understand-vdom-block-structure.md)

### Teleport
- 模态框因父级 CSS 转换而中断 → 请参阅 [teleport-css-positioning-issues](reference/teleport-css-positioning-issues.md)
- 内容在移动设备上需要不同的布局 → 请参阅 [teleport-disabled-for-responsive](reference/teleport-disabled-for-responsive.md)
- 不确定 props/events 是否通过 teleport 工作 → 请参阅 [teleport-logical-hierarchy-preserved](reference/teleport-logical-hierarchy-preserved.md)
- 多个模态框定位到同一容器 → 请参阅 [teleport-multiple-to-same-target](reference/teleport-multiple-to-same-target.md)
- 作用域样式未应用于传送的内容 → 请参阅 [teleport-scoped-styles-limitation](reference/teleport-scoped-styles-limitation.md)

### Suspense
- 想要以编程方式跟踪 Suspense 加载状态 → 请参阅 [suspense-events-for-state-tracking](reference/suspense-events-for-state-tracking.md)
- 在生产应用中规划 Suspense 的使用 → 请参阅 [suspense-experimental-api-stability](reference/suspense-experimental-api-stability.md)
- 将动态组件注册迁移到 Vite → 请参阅 [dynamic-component-registration-vite](reference/dynamic-component-registration-vite.md)
