重复渲染：

- 自身状态更新：组件内部调用 `setState` 或 `useState` 的更新函数，在当前组件对应的 Fiber 节点上创建更新对象并将其添加至该 Fiber 节点的更新队列
- 父组件渲染： React 默认采用父组件更新 → 子组件递归更新的策略，父组件重新渲染时生成新虚拟 DOM 树，其包含子组件的虚拟 DOM 节点，导致所有子组件被标记为需要协调，从而触发子组件重新渲染
- Hooks： `useEffect` & `useMemo` & `useCallback` -> 依赖项变化 + `React.memo` -> 组件 `props` + `shouldComponentUpdate` -> 返回 `true`

性能优化：

1. 自动批处理：React 在事件处理函数中自动批处理更新，合并多次更新为一次
2. `useEffect` & `useRef`：`useEffect` -> 浅比较， `useRef` -> 持久化存储
3. `shouldComponentUpdate` & `PureComponent` & `React.memo` & `useMemo` & `useCallback`

在类组件中，`shouldComponentUpdate` 返回值表示是否渲染，React 在调用 `render` 方法前触发 `shouldComponentUpdate`，默认直接返回 `true` 即 React 重新渲染当前组件及其子组件，我们可以人为控制其返回值决定是否跳过重新渲染以避免不必要的 Diff 和真实 DOM 操作，而 `React.PureComponent` 通过 `props` 和 `state` 的浅比较自动实现 `shouldComponentUpdate` 方法。此外在函数组件中，通过 `React.memo` 和 `useMemo` 或 `useCallback` 实现类似效果，React 默认递归遍历组件树，更新粒度为组件级别的，`React.memo` 为高阶组件，通过浅比较组件的 `props` 以避免不必要的重新渲染，`useMemo` 用于缓存计算结果以避免在每次渲染时重新计算，当依赖项变化时才重新计算，`useCallback` 用于缓存回调函数，当依赖项变化时才更新回调

```js
shouldComponentUpdate(nextProps, nextState) {
	// 当 props 或 state 变化时重新渲染
    return nextProps.value !== this.props.value || nextState.count !== this.state.count;
}
```

4. `useContext` & 自定义 Hook：`useContext` 用于访问 `context` 上下文对象，在组件树上层通过 `Provider` 组件包裹子组件并传递上下文对象的值 `value`，当 `value` 变化时，所有嵌套在 `Provider` 内部的子组件均重新渲染，而上下文对象频繁更新势必导致不必要的重新渲染，因此只传递必要的 `value`，将上下文对象拆分成多个 Context，各个 Context 只负责一部分属性，允许组件只订阅其所需数据，同时自定义 Hook 在上下文对象中提取所需数据，只在相应数据变化时重新渲染组件
5. 生产版本：因为开发版包含额外的调试信息和警告

深比较：通过 `React.memo` 的第二个参数 `areEqual(prevProps, nextProps)` 重载浅比较行为

```js
import { isEqual } from "lodash";

const deepCompare = (prevProps, nextProps) => isEqual(prevProps, nextProps);
export const DeepCompareComponent = React.memo(MyComponent, deepCompare);
```