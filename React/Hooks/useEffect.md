`useEffect` 用于模拟类组件的生命周期方法，它允许在组件渲染到页面后处理副作用（如数据获取、订阅、手动更改 DOM 等），且可以通过它的依赖数组来精确控制副作用的触发时机，它适用于不需要立即更新 DOM 或者其操作对 DOM 布局没有直接影响的场景

###### componentDidMount

在 `useEffect` 的依赖数组中传入一个空数组，确保副作用函数只在组件首次渲染时执行一次，类似于类组件的 `componentDidMount` 生命周期方法

```JSX
useEffect(() => {
	...
}, []);
```

###### componentDidUpdate

在 `useEffect` 的依赖数组中传入特定的状态或属性，副作用函数在组件首次渲染和任何一个依赖项更改时执行，类似于类组件的 `componentDidUpdate` 生命周期方法

```JSX
useEffect(() => {
	...
}, [dependency1, dependency2]);
```

###### componentWillUnmount

`useEffect` 可以返回一个清理函数，该函数会在组件卸载前执行，类似于类组件的 `componentWillUnmount` 生命周期方法

```JSX
useEffect(() => {
	...
	return () => {
		...
	}
}, []);
```
