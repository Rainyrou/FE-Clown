类组件继承 `React.Component` 的原因在于其充当所有类组件的基类，提供类组件所需的一系列方法，让我们构建具有状态管理和生命周期钩子的 React 组件。当 React 渲染一个组件时，首先检查组件的原型链是否有 `React.Component` 判断其是否为类组件

1. 生命周期：`React.Component` 提供一系列生命周期方法允许在组件不同阶段执行特定代码，管理组件的创建、更新和销毁过程
2. 状态管理：`React.Component` 提供 `this.state` 和 `this.setState`，使类组件访问其内部状态，在适当时机更新状态并重新渲染
3. 强制渲染：`React.Component` 提供 `forceUpdate` 方法，允许强制重新渲染组件

React 在底层为每个组件维护一个状态队列，当使用 `useState` 等 Hooks 时，React 将状态添加到该队列中，React 使用索引定位每个 Hooks 的状态，每次重新渲染函数组件时，React 遍历该队列并根据 Hooks 的调用顺序读取状态，以正确维护和更新 Hooks 的状态。此种机制内嵌于 React 的渲染引擎，与类组件依赖的继承机制有所不同