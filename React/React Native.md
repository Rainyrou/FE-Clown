参考

[New Architecture is here · React Native](https://reactnative.dev/blog/2024/10/23/the-new-architecture-is-here#new-features)

React Native 是由 Meta 于 2015 年 9 月发布的一款开源 JavaScript 框架，用于开发跨平台移动应用，它既保留了 React 的开发效率，同时兼具 Native 应用的良好体验，再加上 Virtual DOM 跨平台的天然优势，实现了真正意义上的：Learn Once,Write Anywhere

###### React Native 0.76

该版为 React Native 团队自 2018 年以来致力于重写 React Native 的结果，采用逐步迁移，2021 成立的新架构工作组与社区合作确保 React 生态系统的顺利升级，新架构增加对现代 React 功能的全面支持包括  Suspense、Transitions、自动批处理和  `useLayoutEffect` 及新的原生模块和原生组件系统，无需桥接即可直接访问原生接口

旧架构：

1. JavaScript 线程：即运行于 React Native 中的 JavaScript 代码的地方，React Native 通过 iOS 的 JavaScriptCore 引擎或 Android 的 V8 引擎来执行 JavaScript 代码，这里的 JavaScript 代码使用的是 React 框架，在此线程中，React 根据应用状态计算出需要渲染的 UI 组件树，并通过 Virtual DOM 进行高效的界面更新
2. Native 线程：即处理 iOS 和 Android Native 代码的线程，React Native 应用虽是用 JavaScript 编写的，但最终通过平台的 Native 组件如 iOS 的 UIKit 和 Android 的 View 系统等渲染用户界面，并直接与操作系统和硬件进行交互，因此在运行时需将 JavaScript 描述的 UI 组件树翻译成 Native UI
3. Bridge：基于消息队列的异步双向消息传递通道，用于连接 JavaScript 线程和 Native 线程，异步意味着 JavaScript 可以发起一个操作而无需等待 Native 返回结果，为减少通信开销，React Native 将多个操作打包成批量消息进行发送而不是单个消息逐条发送，同时由于 JavaScript 代码和 Native 代码运行于不同线程，跨线程的数据需进行序列化，JavaScript 代码被 React Native 序列化为 JSON 格式，通过 Bridge 调用 Native 代码，Native 代码接收后将其反序列化并调用对应的 Native 方法包括但不限于 NFC、定位、通知、震动效果、原生存储、网络请求、图片资源访问、图形绘制和 3D 加速等（这些功能通过 Bridge 封装为 JavaScript 接口），通过 Bridge 将调用结果再次序列化为 JSON 格式，返回给 JavaScript 代码，总之通过 Bridge，React Native 将 JavaScript 描述的 UI 组件树映射到 Native 组件，最终实现原生的界面渲染和交互，由于所有工作均在后台线程进行，因此主线程未被阻塞

![[Pasted image 20241108183306.png]]

某些更新需同步渲染以响应用户交互，这可能中断任何正在进行的渲染，由于旧架构是异步的，我们需重写它以允许同步和异步更新，此外旧架构通过 Bridge 序列化函数调用成为性能瓶颈，应用程序难以实现 60+ FPS，且存在同步问题：JavaScript 代码和 Native 代码可能不同步且无法协调而导致错误，如列表显示空白空间帧和由于中间状态渲染而导致的视觉 UI 跳转

在新的原生模块系统中，JavaScript 线程 和 Native 线程通过 JSI 同步通信而无需通过 Bridge 异步通信，这意味着自定义 Native Modules 可同步调用函数并将回调结果返回给另一 Native Module 函数

```js
const value = nativeModule.getValue();
nativeModule.doSomething(value);
```

新的原生模块系统支持用 C++编写的模块，其可实现更细粒度的内存管理和性能优化，适用于所有平台包括 Android、iOS、Windows 和 macOS，仍可通过 JavaScript API 访问。此外根据过往经验，跨边界类型错误是跨平台应用程序中最常见的崩溃来源之一，模块借助  [Codegen](https://reactnative.dev/docs/next/the-new-architecture/what-is-codegen)可在 JavaScript 线程和 Native 线程间定义强类型协定，解决上述问题，同时生成样板代码，最后，模块默认是按需加载的，仅在需要时才加载到内存中而非在启动时加载，减少应用程序启动的时间

新的 Renderer 将视图层次结构存储于不可变的树结构中，即 UI 以无法直接更改的方式存储，从而对更新进行线程安全处理，可跨多个线程处理多个正在进行的 Tree，允许 React Native 在主线程或后台线程上以不同的优先级处理多个并发更新，可中断和复用渲染任务且支持从多个线程同步和异步读取布局。此外其用 C++ 编写的且在所有平台上共享即一套代码多端运行，例如，View Flattening 是一项仅限于 Android 的优化，旨在避免深层嵌套布局树，具有共享 C++ 内核的新的 Renderer 自动将此功能引入 iOS，此种优化无需设置，随共享 Renderer 提供，通过这些新改动，React Native 完全支持 Suspense 和 Transitions 等并发 React 功能，从而可以更轻易地实现复杂的用户界面，而这样的用户界面可以更快速响应用户输入而减少出现卡顿延迟或视觉跳跃的情况。未来，React Native 团队将通过这些新功能为内置组件如 FlatList 和 TextInput 带来更多改进

新的事件循环为 JavaScript 线程上的任务分配不同优先级，允许 React Native 中断渲染以处理事件且与 Web 端规范保持一致，缩小 React Native 和 React DOM 间的差距，即 React Native 应用程序的行为现在更接近 React DOM 应用程序的行为，且允许实现更兼容的浏览器功能如微任务、`MutationObserver`  和  `IntersectionObserver`，这些功能尚未准备好在 React Native 中使用，但 React Native 团队正致力于在未来为大家提供它们。此外事件循环和新的 Renderer 的新改动允许 React Native 支持  `useLayoutEffect`  以同步读取布局信息并在同一帧中更新 UI

移除 Bridge，没有 Bridge 初始化从而缩短启动时间，加快 JavaScript 线程和 Native 线程间通信，将切换工作的成本降至最低，例如在旧架构中，为向 JavaScript 提供全局方法，我们在启动时用 JavaScript 初始化一个模块，导致应用程序启动时出现小延迟，而在新架构中，我们可直接从 C++ 绑定方法。当然 React Native 保持一贯的风格，渐进式升级--Bridge 保持向后兼容性以支持老项目逐步迁移至新架构，未来 React Native 团队将完全删除 Bridge 部分的代码

旧架构：

```js
// ❌ Slow initialization
import { NativeTimingModule } from "NativeTimingModule";
global.setTimeout = (timer) => NativeTimingModule.setTimeout(timer);

// App.js
setTimeout(() => {}, 100);
```

新架构：

```js
// ✅ Initialize directly in C++
runtime.global().setProperty(runtime, "setTimeout", createTimer);

// App.js
setTimeout(() => {}, 100);
```

###### React 和 React Native 的区别

1. 目标平台：

- React：用于开发 Web Applications，主要运行于浏览器，受限于浏览器的功能和特性，React 应用依赖于 Web APIs 如 `window`、`document`、`localStorage` 和 `fetch`，React 通过 JSX 编写代码并最终通过浏览器 DOM 元素来渲染用户界面
- React Native：用于开发 Mobile Applications，主要运行于 iOS 和 Android 原生平台，通过 JSX 编写代码并最终通过平台的 Native 组件渲染用户界面

2. UI 组件

- React：基于 HTML 和 CSS 构建用户界面，React 组件是封装了 HTML 标签和浏览器行为的抽象，通过 JSX 进行组合
- React Native：提供 Native UI 组件的抽象如 iOS 的 `UIView`、`UILabel` 和 Android 的 `View`、`TextView` 及 `Image`，React Native 组件直接映射到对应平台的 Native 控件

3. 样式布局：

- React：通过原生 CSS 等方式来定义样式
- React Native：通过 JavaScript 对象的方式来定义样式，但其不完全遵循 CSS 规范，以无单位数值表示像素，其 `display` 仅支持 `flex` 和 `none`，`position` 仅支持 `relative` 和 `absolute`，不支持 CSS 继承、伪元素、背景图片 `background: url` 和 CSS 动画 `@keyframes` 或 `animation` 属性

4. 渲染方式：

- React：React 自身不处理平台的底层细节，依赖于浏览器的渲染引擎
- React Native：没有 DOM，也不使用 Virtual DOM

5. 导航路由：

- React：通过 React Router 或类似的 Web 路由库来处理 URL 导航
- React Native：由于移动应用没有浏览器的概念，因此 React Native 提供自己的导航系统如 React Navigation，其依赖于移动应用中的 stack 导航和标签导航等模式，模仿 Native 应用的导航体验

6.  打包构建：

- React：通过工具链如 Webpack 和 Babel 等来打包，生成的 HTML、CSS 和 JavaScript 等文件可通过服务器进行部署
- React Native：通过平台构建工具如 Gradle 和 Xcode 等来打包，生成的是 Native 应用的 `.apk`（Android）或 `.ipa`（iOS）文件，JavaScript 代码通过 bundler 打包成一个 JavaScript 文件，Native 代码则打包成相应平台的原生二进制

7. 性能

- React：由于 React 应用运行于浏览器，性能受浏览器限制，尤其是在移动端设备上，可能有性能瓶颈
- React Native：由于 React Native 应用使用的是 Native UI 组件，性能通常比 Web 应用要好

