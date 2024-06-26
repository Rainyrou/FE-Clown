1. `React.memo` & `PureComponent` & `shouldComponentUpdate`

可通过在类组件中实现 `shouldComponentUpdate` 方法来避免不必要的渲染，其返回一个布尔值，决定是否继续进行渲染

```jsx
class MyComponent extends React.Component {
  shouldComponentUpdate(nextProps, nextState) {
    return nextProps.id !== this.props.id;
  }

  render() {}
}
```

`React.PureComponent` 与 `React.Component` 类似，不同之处在于 `PureComponent` 通过 props 和 state 的浅比较自动实现 `shouldComponentUpdate` 方法，若组件的 props 和 state 均不变，那么组件不会进行重新渲染

```JavaScript
class MyComponent extends React.PureComponent {
  render() {}
}
```

2. `useCallback` & `useMemo`

3. 避免在渲染方法或函数组件中直接创建新的对象、数组或函数，否则其会在每次渲染时创建新的引用，即便数据未发生更改，从而导致子组件接收到新的 props 并重新渲染

```JavaScript
render() {
  const style = { color: 'red' };
  const handleClick = () => console.log('clicked');
  return <div style={style} onClick={handleClick}>Click me</div>;
}
```

4. 组件懒加载
