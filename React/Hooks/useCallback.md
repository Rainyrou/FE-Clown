`useCallback` 基于闭包和 React 的 Hooks 机制，它接受一个回调函数和一个依赖项数组，返回一个记忆化的版本的回调函数，只有当依赖项发生变化时，这个回调函数才会更新

```JavaScript
const memoizedCallback = useCallback(() => doSomething(a, b), [a, b]);
```

`useCallback` 利用 React 的 Hooks 机制来存储它的参数---回调函数和依赖项数组。当组件重新渲染时，`useCallback` 检查依赖项数组，若依赖项与上一次渲染时完全相同，则返回上一次渲染时缓存的回调函数，否则 `useCallback` 更新缓存的回调函数并返回新的函数实例
