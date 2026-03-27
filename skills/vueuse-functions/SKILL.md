---
name: vueuse-functions
description: 在 Vue.js / Nuxt 项目中适当地应用 VueUse 组合式函数，以构建简洁、可维护的功能。
compatibility: 需要 Vue 3（及以上）或 Nuxt 3（及以上）项目
---

# VueUse 函数

该技能用于在 Vue.js / Nuxt 项目中选择与实现 VueUse 组合式函数。它将需求映射到最合适的 VueUse 函数，应用正确的使用模式，并优先采用可组合方案而非定制代码，以保持实现简洁、可维护且高性能。

## 何时使用

- 在协助用户进行 Vue.js / Nuxt 开发时均可应用此技能。
- 始终先检查是否可以使用 VueUse 函数满足需求。
- 优先选择 VueUse 组合式函数以提升可读性、可维护性与性能。
- 将需求映射到最合适的 VueUse 函数，并遵循该函数的调用规则。
- 请参考下表中的 `调用方式（Invocation）` 字段，例如：
  - `AUTO`：在适用时自动使用。
  - `EXTERNAL`：仅当用户已安装所需的外部依赖时使用；否则请重新评估，并仅在确有必要时建议安装。
  - `EXPLICIT_ONLY`：仅在用户明确请求时使用。
    > 注意：提示词或 `AGENTS.md` 中的用户指令可能会覆盖函数默认的 `调用方式` 规则。

## 函数列表

以下函数均来自 [VueUse](https://vueuse.org/) 库，每个小节按功能分类。

重要：每条目均包含简短的“说明（Description）”与详细的“参考（Reference）”。使用任意函数时，请查阅 `./references` 中对应文档以了解用法与类型声明。

### 状态

| 函数                                                             | 说明                                                                                                                                                                                                         | 调用方式 |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- |
| [`createGlobalState`](references/createGlobalState.md)           | 在全局作用域保存状态，以便可在多个 Vue 实例间复用                                                                                                                                                            | AUTO     |
| [`createInjectionState`](references/createInjectionState.md)     | 创建可注入到组件中的全局状态                                                                                                                                                                                 | AUTO     |
| [`createSharedComposable`](references/createSharedComposable.md) | 使一个可组合函数可在多个 Vue 实例中共享使用                                                                                                                                                                  | AUTO     |
| [`injectLocal`](references/injectLocal.md)                       | 扩展 `inject`，可在同一组件内调用 `provideLocal` 提供值                                                                                                                                                      | AUTO     |
| [`provideLocal`](references/provideLocal.md)                     | 扩展 `provide`，可在同一组件内调用 `injectLocal` 获取值                                                                                                                                                      | AUTO     |
| [`useAsyncState`](references/useAsyncState.md)                   | 响应式异步状态                                                                                                                                                                                               | AUTO     |
| [`useDebouncedRefHistory`](references/useDebouncedRefHistory.md) | 带防抖过滤的 `useRefHistory` 简写                                                                                                                                                                            | AUTO     |
| [`useLastChanged`](references/useLastChanged.md)                 | 记录最近一次更改的时间戳                                                                                                                                                                                     | AUTO     |
| [`useLocalStorage`](references/useLocalStorage.md)               | 响应式 [LocalStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)                                                                                                                  | AUTO     |
| [`useManualRefHistory`](references/useManualRefHistory.md)       | 通过调用 `commit()` 手动跟踪 ref 的变更历史                                                                                                                                                                  | AUTO     |
| [`useRefHistory`](references/useRefHistory.md)                   | 跟踪 ref 的变更历史                                                                                                                                                                                          | AUTO     |
| [`useSessionStorage`](references/useSessionStorage.md)           | 响应式 [SessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)                                                                                                              | AUTO     |
| [`useStorage`](references/useStorage.md)                         | 创建响应式 ref 以访问与修改 [LocalStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localstorage) 或 [SessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) | AUTO     |
| [`useStorageAsync`](references/useStorageAsync.md)               | 带异步支持的响应式存储                                                                                                                                                                                       | AUTO     |
| [`useThrottledRefHistory`](references/useThrottledRefHistory.md) | 带节流过滤的 `useRefHistory` 简写                                                                                                                                                                            | AUTO     |

### 元素

| 函数                                                               | 说明                                                                                                               | 调用方式 |
| ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------ | -------- |
| [`useActiveElement`](references/useActiveElement.md)               | 响应式 `document.activeElement`                                                                                    | AUTO     |
| [`useDocumentVisibility`](references/useDocumentVisibility.md)     | 响应式跟踪 [`document.visibilityState`](https://developer.mozilla.org/en-US/docs/Web/API/Document/visibilityState) | AUTO     |
| [`useDraggable`](references/useDraggable.md)                       | 使元素可拖拽                                                                                                       | AUTO     |
| [`useDropZone`](references/useDropZone.md)                         | 创建可拖放文件的区域                                                                                               | AUTO     |
| [`useElementBounding`](references/useElementBounding.md)           | 响应式获取 HTML 元素的 [边界矩形](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect)  | AUTO     |
| [`useElementSize`](references/useElementSize.md)                   | 响应式获取 HTML 元素尺寸                                                                                           | AUTO     |
| [`useElementVisibility`](references/useElementVisibility.md)       | 跟踪元素在视口中的可见性                                                                                           | AUTO     |
| [`useIntersectionObserver`](references/useIntersectionObserver.md) | 观察目标元素的可见性变化                                                                                           | AUTO     |
| [`useMouseInElement`](references/useMouseInElement.md)             | 响应式获取相对元素的鼠标位置                                                                                       | AUTO     |
| [`useMutationObserver`](references/useMutationObserver.md)         | 监听 DOM 树的变更                                                                                                  | AUTO     |
| [`useParentElement`](references/useParentElement.md)               | 获取给定元素的父元素                                                                                               | AUTO     |
| [`useResizeObserver`](references/useResizeObserver.md)             | 观察元素内容或边框盒尺寸的变化                                                                                     | AUTO     |
| [`useWindowFocus`](references/useWindowFocus.md)                   | 通过 `window.onfocus` 与 `window.onblur` 响应式跟踪窗口焦点                                                        | AUTO     |
| [`useWindowScroll`](references/useWindowScroll.md)                 | 响应式窗口滚动                                                                                                     | AUTO     |
| [`useWindowSize`](references/useWindowSize.md)                     | 响应式窗口尺寸                                                                                                     | AUTO     |

### 浏览器

| 函数                                                                               | 说明                                                                                                                                                   | 调用方式 |
| ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- |
| [`useBluetooth`](references/useBluetooth.md)                                       | 响应式 [Web Bluetooth API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Bluetooth_API)                                                         | AUTO     |
| [`useBreakpoints`](references/useBreakpoints.md)                                   | 响应式视口断点                                                                                                                                         | AUTO     |
| [`useBroadcastChannel`](references/useBroadcastChannel.md)                         | 响应式 [BroadcastChannel API](https://developer.mozilla.org/en-US/docs/Web/API/BroadcastChannel)                                                       | AUTO     |
| [`useBrowserLocation`](references/useBrowserLocation.md)                           | 响应式浏览器地址                                                                                                                                       | AUTO     |
| [`useClipboard`](references/useClipboard.md)                                       | 响应式 [Clipboard API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API)                                                                 | AUTO     |
| [`useClipboardItems`](references/useClipboardItems.md)                             | 响应式 [Clipboard API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API) 项                                                              | AUTO     |
| [`useColorMode`](references/useColorMode.md)                                       | 响应式颜色模式（暗/亮/自定义），自动数据持久化                                                                                                         | AUTO     |
| [`useCssSupports`](references/useCssSupports.md)                                   | 兼容 SSR 的响应式 [`CSS.supports`](https://developer.mozilla.org/docs/Web/API/CSS/supports_static)                                                     | AUTO     |
| [`useCssVar`](references/useCssVar.md)                                             | 操作 CSS 变量                                                                                                                                          | AUTO     |
| [`useDark`](references/useDark.md)                                                 | 响应式深色模式，自动数据持久化                                                                                                                         | AUTO     |
| [`useEventListener`](references/useEventListener.md)                               | 轻松使用事件监听器                                                                                                                                     | AUTO     |
| [`useEyeDropper`](references/useEyeDropper.md)                                     | 响应式 [EyeDropper API](https://developer.mozilla.org/en-US/docs/Web/API/EyeDropper_API)                                                               | AUTO     |
| [`useFavicon`](references/useFavicon.md)                                           | 响应式网站图标                                                                                                                                         | AUTO     |
| [`useFileDialog`](references/useFileDialog.md)                                     | 轻松打开文件选择器                                                                                                                                     | AUTO     |
| [`useFileSystemAccess`](references/useFileSystemAccess.md)                         | 使用 [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_Access_API) 创建、读取与写入本地文件                        | AUTO     |
| [`useFullscreen`](references/useFullscreen.md)                                     | 响应式 [Fullscreen API](https://developer.mozilla.org/en-US/docs/Web/API/Fullscreen_API)                                                               | AUTO     |
| [`useGamepad`](references/useGamepad.md)                                           | 为 [Gamepad API](https://developer.mozilla.org/en-US/docs/Web/API/Gamepad_API) 提供响应式绑定                                                          | AUTO     |
| [`useImage`](references/useImage.md)                                               | 响应式在浏览器中加载图片                                                                                                                               | AUTO     |
| [`useMediaControls`](references/useMediaControls.md)                               | 为 `audio` 与 `video` 元素提供响应式媒体控制                                                                                                           | AUTO     |
| [`useMediaQuery`](references/useMediaQuery.md)                                     | 响应式 [Media Query](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Testing_media_queries)                                             | AUTO     |
| [`useMemory`](references/useMemory.md)                                             | 响应式内存信息                                                                                                                                         | AUTO     |
| [`useObjectUrl`](references/useObjectUrl.md)                                       | 代表对象的响应式 URL                                                                                                                                   | AUTO     |
| [`usePerformanceObserver`](references/usePerformanceObserver.md)                   | 观察性能指标                                                                                                                                           | AUTO     |
| [`usePermission`](references/usePermission.md)                                     | 响应式 [Permissions API](https://developer.mozilla.org/en-US/docs/Web/API/Permissions_API)                                                             | AUTO     |
| [`usePreferredColorScheme`](references/usePreferredColorScheme.md)                 | 响应式 [prefers-color-scheme](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme) 媒体查询                                   | AUTO     |
| [`usePreferredContrast`](references/usePreferredContrast.md)                       | 响应式 [prefers-contrast](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-contrast) 媒体查询                                           | AUTO     |
| [`usePreferredDark`](references/usePreferredDark.md)                               | 响应式深色主题偏好                                                                                                                                     | AUTO     |
| [`usePreferredLanguages`](references/usePreferredLanguages.md)                     | 响应式 [Navigator Languages](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorLanguage/languages)                                             | AUTO     |
| [`usePreferredReducedMotion`](references/usePreferredReducedMotion.md)             | 响应式 [prefers-reduced-motion](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) 媒体查询                               | AUTO     |
| [`usePreferredReducedTransparency`](references/usePreferredReducedTransparency.md) | 响应式 [prefers-reduced-transparency](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-transparency) 媒体查询                   | AUTO     |
| [`useScreenOrientation`](references/useScreenOrientation.md)                       | 响应式 [Screen Orientation API](https://developer.mozilla.org/en-US/docs/Web/API/Screen_Orientation_API)                                               | AUTO     |
| [`useScreenSafeArea`](references/useScreenSafeArea.md)                             | 响应式 `env(safe-area-inset-*)`                                                                                                                        | AUTO     |
| [`useScriptTag`](references/useScriptTag.md)                                       | 创建 script 标签                                                                                                                                       | AUTO     |
| [`useShare`](references/useShare.md)                                               | 响应式 [Web Share API](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/share)                                                               | AUTO     |
| [`useSSRWidth`](references/useSSRWidth.md)                                         | 设置 SSR 组件在依赖视口宽度（如 [`useMediaQuery`](../useMediaQuery/index.md) 或 [`useBreakpoints`](../useBreakpoints/index.md)）时所使用的全局视口宽度 | AUTO     |
| [`useStyleTag`](references/useStyleTag.md)                                         | 在 head 注入响应式 `style` 元素                                                                                                                        | AUTO     |
| [`useTextareaAutosize`](references/useTextareaAutosize.md)                         | 根据内容自动更新 textarea 的高度                                                                                                                       | AUTO     |
| [`useTextDirection`](references/useTextDirection.md)                               | 元素文本的响应式 [dir](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/dir)                                                        | AUTO     |
| [`useTitle`](references/useTitle.md)                                               | 响应式文档标题                                                                                                                                         | AUTO     |
| [`useUrlSearchParams`](references/useUrlSearchParams.md)                           | 响应式 [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)                                                             | AUTO     |
| [`useVibrate`](references/useVibrate.md)                                           | 响应式 [Vibration API](https://developer.mozilla.org/en-US/docs/Web/API/Vibration_API)                                                                 | AUTO     |
| [`useWakeLock`](references/useWakeLock.md)                                         | 响应式 [Screen Wake Lock API](https://developer.mozilla.org/en-US/docs/Web/API/Screen_Wake_Lock_API)                                                   | AUTO     |
| [`useWebNotification`](references/useWebNotification.md)                           | 响应式 [Notification](https://developer.mozilla.org/en-US/docs/Web/API/notification)                                                                   | AUTO     |
| [`useWebWorker`](references/useWebWorker.md)                                       | 简化 [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 的注册与通信                                    | AUTO     |
| [`useWebWorkerFn`](references/useWebWorkerFn.md)                                   | 在不阻塞 UI 的情况下运行耗时函数                                                                                                                       | AUTO     |

### 传感与输入

| 函数                                                         | 说明                                                                                                                                  | 调用方式 |
| ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| [`onClickOutside`](references/onClickOutside.md)             | 监听元素外部点击                                                                                                                      | AUTO     |
| [`onElementRemoval`](references/onElementRemoval.md)         | 当元素或其父级从 DOM 中被移除时触发                                                                                                   | AUTO     |
| [`onKeyStroke`](references/onKeyStroke.md)                   | 监听键盘按键                                                                                                                          | AUTO     |
| [`onLongPress`](references/onLongPress.md)                   | 监听元素长按                                                                                                                          | AUTO     |
| [`onStartTyping`](references/onStartTyping.md)               | 当用户在不可编辑元素上开始输入时触发                                                                                                  | AUTO     |
| [`useBattery`](references/useBattery.md)                     | 响应式 [Battery Status API](https://developer.mozilla.org/en-US/docs/Web/API/Battery_Status_API)                                      | AUTO     |
| [`useDeviceMotion`](references/useDeviceMotion.md)           | 响应式 [DeviceMotionEvent](https://developer.mozilla.org/en-US/docs/Web/API/DeviceMotionEvent)                                        | AUTO     |
| [`useDeviceOrientation`](references/useDeviceOrientation.md) | 响应式 [DeviceOrientationEvent](https://developer.mozilla.org/en-US/docs/Web/API/DeviceOrientationEvent)                              | AUTO     |
| [`useDevicePixelRatio`](references/useDevicePixelRatio.md)   | 响应式跟踪 [`window.devicePixelRatio`](https://developer.mozilla.org/docs/Web/API/Window/devicePixelRatio)                            | AUTO     |
| [`useDevicesList`](references/useDevicesList.md)             | 响应式列出可用的输入/输出设备（[`enumerateDevices`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/enumerateDevices)） | AUTO     |
| [`useDisplayMedia`](references/useDisplayMedia.md)           | 响应式屏幕分享流（[`getDisplayMedia`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getDisplayMedia)）                | AUTO     |
| [`useElementByPoint`](references/useElementByPoint.md)       | 响应式按坐标获取元素                                                                                                                  | AUTO     |
| [`useElementHover`](references/useElementHover.md)           | 响应式元素悬停状态                                                                                                                    | AUTO     |
| [`useFocus`](references/useFocus.md)                         | 跟踪或设置 DOM 元素焦点状态的响应式工具                                                                                               | AUTO     |
| [`useFocusWithin`](references/useFocusWithin.md)             | 跟踪元素或其子孙是否获得焦点的响应式工具                                                                                              | AUTO     |
| [`useFps`](references/useFps.md)                             | 响应式 FPS（每秒帧数）                                                                                                                | AUTO     |
| [`useGeolocation`](references/useGeolocation.md)             | 响应式 [Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)                                            | AUTO     |
| [`useIdle`](references/useIdle.md)                           | 跟踪用户是否处于空闲                                                                                                                  | AUTO     |
| [`useInfiniteScroll`](references/useInfiniteScroll.md)       | 元素无限滚动                                                                                                                          | AUTO     |
| [`useKeyModifier`](references/useKeyModifier.md)             | 响应式 [修饰键状态](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/getModifierState)                                  | AUTO     |
| [`useMagicKeys`](references/useMagicKeys.md)                 | 响应式按键按下状态                                                                                                                    | AUTO     |
| [`useMouse`](references/useMouse.md)                         | 响应式鼠标位置                                                                                                                        | AUTO     |
| [`useMousePressed`](references/useMousePressed.md)           | 响应式鼠标按压状态                                                                                                                    | AUTO     |
| [`useNavigatorLanguage`](references/useNavigatorLanguage.md) | 响应式 [`navigator.language`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/language)                                    | AUTO     |
| [`useNetwork`](references/useNetwork.md)                     | 响应式 [网络状态](https://developer.mozilla.org/en-US/docs/Web/API/Network_Information_API)                                           | AUTO     |
| [`useOnline`](references/useOnline.md)                       | 响应式在线状态                                                                                                                        | AUTO     |
| [`usePageLeave`](references/usePageLeave.md)                 | 响应式检测鼠标是否离开页面                                                                                                            | AUTO     |
| [`useParallax`](references/useParallax.md)                   | 轻松创建视差效果                                                                                                                      | AUTO     |
| [`usePointer`](references/usePointer.md)                     | 响应式 [指针状态](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events)                                                    | AUTO     |
| [`usePointerLock`](references/usePointerLock.md)             | 响应式 [指针锁定](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_Lock_API)                                                  | AUTO     |
| [`usePointerSwipe`](references/usePointerSwipe.md)           | 基于 [PointerEvents](https://developer.mozilla.org/en-US/docs/Web/API/PointerEvent) 的响应式滑动检测                                  | AUTO     |
| [`useScroll`](references/useScroll.md)                       | 响应式滚动位置与状态                                                                                                                  | AUTO     |
| [`useScrollLock`](references/useScrollLock.md)               | 锁定元素滚动                                                                                                                          | AUTO     |
| [`useSpeechRecognition`](references/useSpeechRecognition.md) | 响应式 [SpeechRecognition](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition)                                        | AUTO     |
| [`useSpeechSynthesis`](references/useSpeechSynthesis.md)     | 响应式 [SpeechSynthesis](https://developer.mozilla.org/en-US/docs/Web/API/SpeechSynthesis)                                            | AUTO     |
| [`useSwipe`](references/useSwipe.md)                         | 基于 [`TouchEvents`](https://developer.mozilla.org/en-US/docs/Web/API/TouchEvent) 的响应式滑动检测                                    | AUTO     |
| [`useTextSelection`](references/useTextSelection.md)         | 基于 [`Window.getSelection`](https://developer.mozilla.org/en-US/docs/Web/API/Window/getSelection) 响应式跟踪用户的文本选中           | AUTO     |
| [`useUserMedia`](references/useUserMedia.md)                 | 响应式媒体采集流（[`getUserMedia`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia)）                      | AUTO     |

### 网络

| 函数                                             | 说明                                                                                                                                                                                                    | 调用方式 |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| [`useEventSource`](references/useEventSource.md) | 使用 [EventSource](https://developer.mozilla.org/en-US/docs/Web/API/EventSource) / [Server-Sent-Events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events) 与 HTTP 服务器建立持久连接 | AUTO     |
| [`useFetch`](references/useFetch.md)             | 响应式 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)，支持中止请求                                                                                                            | AUTO     |
| [`useWebSocket`](references/useWebSocket.md)     | 响应式 [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket/WebSocket) 客户端                                                                                                         | AUTO     |

### 动画

| 函数                                           | 说明                                                                                             | 调用方式 |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------ | -------- |
| [`useAnimate`](references/useAnimate.md)       | 响应式 [Web Animations API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API) | AUTO     |
| [`useInterval`](references/useInterval.md)     | 响应式计数器（每个间隔递增）                                                                     | AUTO     |
| [`useIntervalFn`](references/useIntervalFn.md) | 带控制的 `setInterval` 包装                                                                      | AUTO     |
| [`useNow`](references/useNow.md)               | 响应式当前 `Date` 实例                                                                           | AUTO     |
| [`useRafFn`](references/useRafFn.md)           | 每次 `requestAnimationFrame` 调用函数                                                            | AUTO     |
| [`useTimeout`](references/useTimeout.md)       | 指定时间后变为 `true` 的响应式值                                                                 | AUTO     |
| [`useTimeoutFn`](references/useTimeoutFn.md)   | 带控制的 `setTimeout` 包装                                                                       | AUTO     |
| [`useTimestamp`](references/useTimestamp.md)   | 响应式当前时间戳                                                                                 | AUTO     |
| [`useTransition`](references/useTransition.md) | 值之间的过渡                                                                                     | AUTO     |

### 组件

| 函数                                                             | 说明                                             | 调用方式 |
| ---------------------------------------------------------------- | ------------------------------------------------ | -------- |
| [`computedInject`](references/computedInject.md)                 | 组合使用 `computed` 与 `inject`                  | AUTO     |
| [`createReusableTemplate`](references/createReusableTemplate.md) | 在组件作用域内定义并复用模板                     | AUTO     |
| [`createTemplatePromise`](references/createTemplatePromise.md)   | 将模板用作 Promise                               | AUTO     |
| [`templateRef`](references/templateRef.md)                       | 绑定模板元素 ref 的简写                          | AUTO     |
| [`tryOnBeforeMount`](references/tryOnBeforeMount.md)             | 安全的 `onBeforeMount`                           | AUTO     |
| [`tryOnBeforeUnmount`](references/tryOnBeforeUnmount.md)         | 安全的 `onBeforeUnmount`                         | AUTO     |
| [`tryOnMounted`](references/tryOnMounted.md)                     | 安全的 `onMounted`                               | AUTO     |
| [`tryOnScopeDispose`](references/tryOnScopeDispose.md)           | 安全的 `onScopeDispose`                          | AUTO     |
| [`tryOnUnmounted`](references/tryOnUnmounted.md)                 | 安全的 `onUnmounted`                             | AUTO     |
| [`unrefElement`](references/unrefElement.md)                     | 从 Vue ref 或组件实例获取底层 DOM 元素           | AUTO     |
| [`useCurrentElement`](references/useCurrentElement.md)           | 以 ref 形式获取当前组件的 DOM 元素               | AUTO     |
| [`useMounted`](references/useMounted.md)                         | 以 ref 表示的挂载状态                            | AUTO     |
| [`useTemplateRefsList`](references/useTemplateRefsList.md)       | 在 `v-for` 中为模板元素或组件绑定多个 ref 的简写 | AUTO     |
| [`useVirtualList`](references/useVirtualList.md)                 | 轻松创建虚拟列表                                 | AUTO     |
| [`useVModel`](references/useVModel.md)                           | `v-model` 绑定的简写                             | AUTO     |
| [`useVModels`](references/useVModels.md)                         | `props` 的 `v-model` 绑定简写                    | AUTO     |

### 监听

| 函数                                                 | 说明                                  | 调用方式 |
| ---------------------------------------------------- | ------------------------------------- | -------- |
| [`until`](references/until.md)                       | 基于 Promise 的一次性变更监听         | AUTO     |
| [`watchArray`](references/watchArray.md)             | 监听数组的新增与移除                  | AUTO     |
| [`watchAtMost`](references/watchAtMost.md)           | 可限制触发次数的 `watch`              | AUTO     |
| [`watchDebounced`](references/watchDebounced.md)     | 防抖的 `watch`                        | AUTO     |
| [`watchDeep`](references/watchDeep.md)               | 监听深度变化的简写（`{deep: true}`）  | AUTO     |
| [`watchIgnorable`](references/watchIgnorable.md)     | 可忽略触发的 `watch`                  | AUTO     |
| [`watchImmediate`](references/watchImmediate.md)     | 立即执行的简写（`{immediate: true}`） | AUTO     |
| [`watchOnce`](references/watchOnce.md)               | 仅触发一次的简写（`{ once: true }`）  | AUTO     |
| [`watchPausable`](references/watchPausable.md)       | 可暂停的 `watch`                      | AUTO     |
| [`watchThrottled`](references/watchThrottled.md)     | 节流的 `watch`                        | AUTO     |
| [`watchTriggerable`](references/watchTriggerable.md) | 可手动触发的 `watch`                  | AUTO     |
| [`watchWithFilter`](references/watchWithFilter.md)   | 带 `EventFilter` 控制的 `watch`       | AUTO     |
| [`whenever`](references/whenever.md)                 | 监听值为真时的简写                    | AUTO     |

### 响应式

| 函数                                                       | 说明                                                                                        | 调用方式      |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------------------- | ------------- |
| [`computedAsync`](references/computedAsync.md)             | 面向异步函数的 `computed`                                                                   | AUTO          |
| [`computedEager`](references/computedEager.md)             | 非惰性计算的 `computed`                                                                     | AUTO          |
| [`computedWithControl`](references/computedWithControl.md) | 显式定义 `computed` 的依赖                                                                  | AUTO          |
| [`createRef`](references/createRef.md)                     | 根据 `deep` 参数返回 `deepRef` 或 `shallowRef`                                              | AUTO          |
| [`extendRef`](references/extendRef.md)                     | 为 Ref 添加额外属性                                                                         | AUTO          |
| [`reactify`](references/reactify.md)                       | 将普通函数转换为响应式函数                                                                  | AUTO          |
| [`reactifyObject`](references/reactifyObject.md)           | 对对象应用 `reactify`                                                                       | AUTO          |
| [`reactiveComputed`](references/reactiveComputed.md)       | 响应式对象的 `computed`                                                                     | AUTO          |
| [`reactiveOmit`](references/reactiveOmit.md)               | 响应式地从对象中省略字段                                                                    | AUTO          |
| [`reactivePick`](references/reactivePick.md)               | 响应式地从对象中挑选字段                                                                    | AUTO          |
| [`refAutoReset`](references/refAutoReset.md)               | 一段时间后自动重置的 ref                                                                    | AUTO          |
| [`refDebounced`](references/refDebounced.md)               | 防抖变化的 ref                                                                              | AUTO          |
| [`refDefault`](references/refDefault.md)                   | 为 ref 应用默认值                                                                           | AUTO          |
| [`refManualReset`](references/refManualReset.md)           | 带手动重置功能的 ref                                                                        | AUTO          |
| [`refThrottled`](references/refThrottled.md)               | 节流变化的 ref                                                                              | AUTO          |
| [`refWithControl`](references/refWithControl.md)           | 对 ref 及其响应性进行细粒度控制                                                             | AUTO          |
| [`syncRef`](references/syncRef.md)                         | 双向同步多个 ref                                                                            | AUTO          |
| [`syncRefs`](references/syncRefs.md)                       | 使目标 ref 与源 ref 保持同步                                                                | AUTO          |
| [`toReactive`](references/toReactive.md)                   | 将 ref 转为 `reactive` 对象                                                                 | AUTO          |
| [`toRef`](references/toRef.md)                             | 将 值 / ref / getter 规范化为 `ref` 或 `computed`                                           | EXPLICIT_ONLY |
| [`toRefs`](references/toRefs.md)                           | 扩展的 [`toRefs`](https://vuejs.org/api/reactivity-utilities.html#torefs)，可接受对象的 ref | AUTO          |

### 数组

| 函数                                                     | 说明                     | 调用方式 |
| -------------------------------------------------------- | ------------------------ | -------- |
| [`useArrayDifference`](references/useArrayDifference.md) | 响应式获取两个数组的差集 | AUTO     |
| [`useArrayEvery`](references/useArrayEvery.md)           | 响应式 `Array.every`     | AUTO     |
| [`useArrayFilter`](references/useArrayFilter.md)         | 响应式 `Array.filter`    | AUTO     |
| [`useArrayFind`](references/useArrayFind.md)             | 响应式 `Array.find`      | AUTO     |
| [`useArrayFindIndex`](references/useArrayFindIndex.md)   | 响应式 `Array.findIndex` | AUTO     |
| [`useArrayFindLast`](references/useArrayFindLast.md)     | 响应式 `Array.findLast`  | AUTO     |
| [`useArrayIncludes`](references/useArrayIncludes.md)     | 响应式 `Array.includes`  | AUTO     |
| [`useArrayJoin`](references/useArrayJoin.md)             | 响应式 `Array.join`      | AUTO     |
| [`useArrayMap`](references/useArrayMap.md)               | 响应式 `Array.map`       | AUTO     |
| [`useArrayReduce`](references/useArrayReduce.md)         | 响应式 `Array.reduce`    | AUTO     |
| [`useArraySome`](references/useArraySome.md)             | 响应式 `Array.some`      | AUTO     |
| [`useArrayUnique`](references/useArrayUnique.md)         | 响应式唯一数组           | AUTO     |
| [`useSorted`](references/useSorted.md)                   | 响应式排序数组           | AUTO     |

### 时间

| 函数                                             | 说明                               | 调用方式 |
| ------------------------------------------------ | ---------------------------------- | -------- |
| [`useCountdown`](references/useCountdown.md)     | 响应式以秒计的倒计时               | AUTO     |
| [`useDateFormat`](references/useDateFormat.md)   | 根据传入的令牌字符串获取格式化日期 | AUTO     |
| [`useTimeAgo`](references/useTimeAgo.md)         | 响应式“多久之前”时间               | AUTO     |
| [`useTimeAgoIntl`](references/useTimeAgoIntl.md) | 支持 i18n 的响应式“多久之前”时间   | AUTO     |

### 工具

| 函数                                                       | 说明                                               | 调用方式      |
| ---------------------------------------------------------- | -------------------------------------------------- | ------------- |
| [`createEventHook`](references/createEventHook.md)         | 创建事件钩子的工具                                 | AUTO          |
| [`createUnrefFn`](references/createUnrefFn.md)             | 使普通函数可以同时接受 ref 与原始值作为参数        | AUTO          |
| [`get`](references/get.md)                                 | 访问 `ref.value` 的简写                            | EXPLICIT_ONLY |
| [`isDefined`](references/isDefined.md)                     | Ref 的非空类型守卫                                 | AUTO          |
| [`makeDestructurable`](references/makeDestructurable.md)   | 让对象与数组同时支持解构                           | AUTO          |
| [`set`](references/set.md)                                 | `ref.value = x` 的简写                             | EXPLICIT_ONLY |
| [`useAsyncQueue`](references/useAsyncQueue.md)             | 依次执行异步任务并将当前任务的结果传递给下一个任务 | AUTO          |
| [`useBase64`](references/useBase64.md)                     | 响应式 Base64 转换                                 | AUTO          |
| [`useCached`](references/useCached.md)                     | 使用自定义比较器缓存一个 ref                       | AUTO          |
| [`useCloned`](references/useCloned.md)                     | ref 的响应式克隆                                   | AUTO          |
| [`useConfirmDialog`](references/useConfirmDialog.md)       | 创建事件钩子以支持模态框与确认对话的链式调用       | AUTO          |
| [`useCounter`](references/useCounter.md)                   | 带实用函数的基础计数器                             | AUTO          |
| [`useCycleList`](references/useCycleList.md)               | 在项目列表中循环                                   | AUTO          |
| [`useDebounceFn`](references/useDebounceFn.md)             | 对函数执行进行防抖                                 | AUTO          |
| [`useEventBus`](references/useEventBus.md)                 | 基础事件总线                                       | AUTO          |
| [`useMemoize`](references/useMemoize.md)                   | 按参数缓存函数结果并保持响应式                     | AUTO          |
| [`useOffsetPagination`](references/useOffsetPagination.md) | 响应式偏移分页                                     | AUTO          |
| [`usePrevious`](references/usePrevious.md)                 | 保存 ref 的前一个值                                | AUTO          |
| [`useStepper`](references/useStepper.md)                   | 提供构建多步骤向导界面的辅助工具                   | AUTO          |
| [`useSupported`](references/useSupported.md)               | SSR 兼容的 `isSupported` 检查                      | AUTO          |
| [`useThrottleFn`](references/useThrottleFn.md)             | 对函数执行进行节流                                 | AUTO          |
| [`useTimeoutPoll`](references/useTimeoutPoll.md)           | 使用超时进行轮询                                   | AUTO          |
| [`useToggle`](references/useToggle.md)                     | 带实用函数的布尔开关                               | AUTO          |
| [`useToNumber`](references/useToNumber.md)                 | 响应式地将字符串 ref 转为数字                      | AUTO          |
| [`useToString`](references/useToString.md)                 | 响应式地将 ref 转为字符串                          | AUTO          |

### @Electron

| 函数                                                         | 说明                                                                                                                                                                                                                                                   | 调用方式 |
| ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- |
| [`useIpcRenderer`](references/useIpcRenderer.md)             | 提供 [ipcRenderer](https://www.electronjs.org/docs/api/ipc-renderer) 及其全部 API 的 Vue 响应式封装                                                                                                                                                    | EXTERNAL |
| [`useIpcRendererInvoke`](references/useIpcRendererInvoke.md) | 响应式获取 [ipcRenderer.invoke API](https://www.electronjs.org/docs/api/ipc-renderer#ipcrendererinvokechannel-args) 的结果                                                                                                                             | EXTERNAL |
| [`useIpcRendererOn`](references/useIpcRendererOn.md)         | 便捷使用 [ipcRenderer.on](https://www.electronjs.org/docs/api/ipc-renderer#ipcrendereronchannel-listener)，并在卸载时自动调用 [ipcRenderer.removeListener](https://www.electronjs.org/docs/api/ipc-renderer#ipcrendererremovelistenerchannel-listener) | EXTERNAL |
| [`useZoomFactor`](references/useZoomFactor.md)               | 响应式 [WebFrame](https://www.electronjs.org/docs/api/web-frame#webframe) 缩放因子                                                                                                                                                                     | EXTERNAL |
| [`useZoomLevel`](references/useZoomLevel.md)                 | 响应式 [WebFrame](https://www.electronjs.org/docs/api/web-frame#webframe) 缩放级别                                                                                                                                                                     | EXTERNAL |

### @Firebase

| 函数                                         | 说明                                                                                | 调用方式 |
| -------------------------------------------- | ----------------------------------------------------------------------------------- | -------- |
| [`useAuth`](references/useAuth.md)           | 响应式 [Firebase Auth](https://firebase.google.com/docs/auth) 绑定                  | EXTERNAL |
| [`useFirestore`](references/useFirestore.md) | 响应式 [Firestore](https://firebase.google.com/docs/firestore) 绑定                 | EXTERNAL |
| [`useRTDB`](references/useRTDB.md)           | 响应式 [Firebase Realtime Database](https://firebase.google.com/docs/database) 绑定 | EXTERNAL |

### @Head

| 函数                                               | 说明                   | 调用方式 |
| -------------------------------------------------- | ---------------------- | -------- |
| [`createHead`](https://github.com/vueuse/head#api) | 创建 head 管理器实例   | EXTERNAL |
| [`useHead`](https://github.com/vueuse/head#api)    | 响应式更新 head 元标签 | EXTERNAL |

### @Integrations

| 函数                                                   | 说明                                                                        | 调用方式 |
| ------------------------------------------------------ | --------------------------------------------------------------------------- | -------- |
| [`useAsyncValidator`](references/useAsyncValidator.md) | [`async-validator`](https://github.com/yiminghe/async-validator) 的包装     | EXTERNAL |
| [`useAxios`](references/useAxios.md)                   | [`axios`](https://github.com/axios/axios) 的包装                            | EXTERNAL |
| [`useChangeCase`](references/useChangeCase.md)         | [`change-case`](https://github.com/blakeembrey/change-case) 的响应式封装    | EXTERNAL |
| [`useCookies`](references/useCookies.md)               | [`universal-cookie`](https://www.npmjs.com/package/universal-cookie) 的包装 | EXTERNAL |
| [`useDrauu`](references/useDrauu.md)                   | [drauu](https://github.com/antfu/drauu) 的响应式实例                        | EXTERNAL |
| [`useFocusTrap`](references/useFocusTrap.md)           | [`focus-trap`](https://github.com/focus-trap/focus-trap) 的响应式封装       | EXTERNAL |
| [`useFuse`](references/useFuse.md)                     | 使用 [Fuse.js](https://github.com/krisk/fuse) 通过组合式轻松实现模糊搜索    | EXTERNAL |
| [`useIDBKeyval`](references/useIDBKeyval.md)           | [`idb-keyval`](https://www.npmjs.com/package/idb-keyval) 的包装             | EXTERNAL |
| [`useJwt`](references/useJwt.md)                       | [`jwt-decode`](https://github.com/auth0/jwt-decode) 的包装                  | EXTERNAL |
| [`useNProgress`](references/useNProgress.md)           | [`nprogress`](https://github.com/rstacruz/nprogress) 的响应式封装           | EXTERNAL |
| [`useQRCode`](references/useQRCode.md)                 | [`qrcode`](https://github.com/soldair/node-qrcode) 的包装                   | EXTERNAL |
| [`useSortable`](references/useSortable.md)             | [`sortable`](https://github.com/SortableJS/Sortable) 的包装                 | EXTERNAL |

### @Math

| 函数                                                               | 说明                                 | 调用方式 |
| ------------------------------------------------------------------ | ------------------------------------ | -------- |
| [`createGenericProjection`](references/createGenericProjection.md) | `createProjection` 的通用版本        | EXTERNAL |
| [`createProjection`](references/createProjection.md)               | 响应式数值投射（从一个域到另一个域） | EXTERNAL |
| [`logicAnd`](references/logicAnd.md)                               | ref 的 AND 条件                      | EXTERNAL |
| [`logicNot`](references/logicNot.md)                               | ref 的 NOT 条件                      | EXTERNAL |
| [`logicOr`](references/logicOr.md)                                 | ref 的 OR 条件                       | EXTERNAL |
| [`useAbs`](references/useAbs.md)                                   | 响应式 `Math.abs`                    | EXTERNAL |
| [`useAverage`](references/useAverage.md)                           | 响应式获取数组平均值                 | EXTERNAL |
| [`useCeil`](references/useCeil.md)                                 | 响应式 `Math.ceil`                   | EXTERNAL |
| [`useClamp`](references/useClamp.md)                               | 响应式将值限制在两个值之间           | EXTERNAL |
| [`useFloor`](references/useFloor.md)                               | 响应式 `Math.floor`                  | EXTERNAL |
| [`useMath`](references/useMath.md)                                 | 响应式 `Math` 方法                   | EXTERNAL |
| [`useMax`](references/useMax.md)                                   | 响应式 `Math.max`                    | EXTERNAL |
| [`useMin`](references/useMin.md)                                   | 响应式 `Math.min`                    | EXTERNAL |
| [`usePrecision`](references/usePrecision.md)                       | 响应式设置数字精度                   | EXTERNAL |
| [`useProjection`](references/useProjection.md)                     | 响应式数值投射（从一个域到另一个域） | EXTERNAL |
| [`useRound`](references/useRound.md)                               | 响应式 `Math.round`                  | EXTERNAL |
| [`useSum`](references/useSum.md)                                   | 响应式获取数组求和                   | EXTERNAL |
| [`useTrunc`](references/useTrunc.md)                               | 响应式 `Math.trunc`                  | EXTERNAL |

### @Motion

| 函数                                                                         | 说明                                  | 调用方式 |
| ---------------------------------------------------------------------------- | ------------------------------------- | -------- |
| [`useElementStyle`](https://motion.vueuse.org/api/use-element-style)         | 将响应式对象同步到目标元素的 CSS 样式 | EXTERNAL |
| [`useElementTransform`](https://motion.vueuse.org/api/use-element-transform) | 将响应式对象同步到目标元素的 CSS 变换 | EXTERNAL |
| [`useMotion`](https://motion.vueuse.org/api/use-motion)                      | 让你的组件动起来                      | EXTERNAL |
| [`useMotionProperties`](https://motion.vueuse.org/api/use-motion-properties) | 访问目标元素的 Motion 属性            | EXTERNAL |
| [`useMotionVariants`](https://motion.vueuse.org/api/use-motion-variants)     | 管理 Variants 状态与选择              | EXTERNAL |
| [`useSpring`](https://motion.vueuse.org/api/use-spring)                      | 弹簧动画                              | EXTERNAL |

### @Router

| 函数                                             | 说明                         | 调用方式 |
| ------------------------------------------------ | ---------------------------- | -------- |
| [`useRouteHash`](references/useRouteHash.md)     | 响应式 `route.hash` 的简写   | EXTERNAL |
| [`useRouteParams`](references/useRouteParams.md) | 响应式 `route.params` 的简写 | EXTERNAL |
| [`useRouteQuery`](references/useRouteQuery.md)   | 响应式 `route.query` 的简写  | EXTERNAL |

### @RxJS

| 函数                                                                 | 说明                                                                                                                                                         | 调用方式 |
| -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- |
| [`from`](references/from.md)                                         | 针对 RxJS 的 [`from()`](https://rxjs.dev/api/index/function/from) 与 [`fromEvent()`](https://rxjs.dev/api/index/function/fromEvent) 的包装，使其可接受 `ref` | EXTERNAL |
| [`toObserver`](references/toObserver.md)                             | 将 `ref` 转为 RxJS [Observer](https://rxjs.dev/guide/observer) 的辅助函数                                                                                    | EXTERNAL |
| [`useExtractedObservable`](references/useExtractedObservable.md)     | 使用从一个或多个组合式中提取的 RxJS [`Observable`](https://rxjs.dev/guide/observable)                                                                        | EXTERNAL |
| [`useObservable`](references/useObservable.md)                       | 使用 RxJS [`Observable`](https://rxjs.dev/guide/observable)                                                                                                  | EXTERNAL |
| [`useSubject`](references/useSubject.md)                             | 将 RxJS [`Subject`](https://rxjs.dev/guide/subject) 绑定到 `ref`，并实现双向值变化传递                                                                       | EXTERNAL |
| [`useSubscription`](references/useSubscription.md)                   | 使用 RxJS [`Subscription`](https://rxjs.dev/guide/subscription) 而无需担心退订或内存泄漏                                                                     | EXTERNAL |
| [`watchExtractedObservable`](references/watchExtractedObservable.md) | 监听从一个或多个组合式中提取的 RxJS [`Observable`](https://rxjs.dev/guide/observable) 的值                                                                   | EXTERNAL |

### @SchemaOrg

| 函数                                                                                    | 说明                       | 调用方式 |
| --------------------------------------------------------------------------------------- | -------------------------- | -------- |
| [`createSchemaOrg`](https://vue-schema-org.netlify.app/api/core/create-schema-org.html) | 创建 schema.org 管理器实例 | EXTERNAL |
| [`useSchemaOrg`](https://vue-schema-org.netlify.app/api/core/use-schema-org.html)       | 响应式更新 schema.org      | EXTERNAL |

### @Sound

| 函数                                                   | 说明           | 调用方式 |
| ------------------------------------------------------ | -------------- | -------- |
| [`useSound`](https://github.com/vueuse/sound#examples) | 响应式播放音效 | EXTERNAL |
