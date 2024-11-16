1. `PureComponent` & `shouldComponentUpdate`

在类组件中使用 `shouldComponentUpdate` 方法来避免不必要的渲染，其返回一个布尔值，表示是否继续进行渲染。`React.PureComponent` 与 `React.Component` 类似，但 `PureComponent` 通过 props 和 state 的浅比较自动实现 `shouldComponentUpdate` 方法

2. 控制组件的重新渲染范围：React 的默认行为是递归遍历组件树，更新粒度为组件级别的，`React.memo` 为高阶组件，对组件的 `props` 进行浅比较，跳过不必要的重新渲染
3. 避免组件入参不必要变化：`useMemo` + `useCallback` + `useContext`，若上下文对象频繁变化势必触发大量不必要渲染，确保传递给 `Provider` 的 `value` 只在必要时改变，同时将将上下文对象拆分成多个 Context，每个 Context 只负责一部分属性，允许组件只订阅它们关注的部分，或通过 `React.memo` 对不依赖上下文对象变化的组件进行性能优化，自定义 Hook 在上下文对象中提取所需数据，其只在相应属性变化时重新渲染
4. 避免无意义的状态更新：`useState` 的状态影响组件重新渲染，因此用 `useRef` 存储不影响 UI 的变量