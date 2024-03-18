1. `React.memo` & `PureComponent` + `shouldComponentUpdate`

`React.memo` 仅对其组件的 props 进行浅比较，当函数组件接收到相同的 props 时，`React.memo` 跳过渲染，从而提高性能，同时它接受第二个参数作为自定义比较函数

```JavaScript
const MyComponent = React.memo(function MyComponent(props) {});

function YourComponent(props) {}
function areEqual(prevProps, nextProps) {}

export default React.memo(YourComponent, areEqual);
```

`PureComponent` 在类组件中通过 `shouldComponentUpdate` 进行浅比较来防止不必要的渲染

```JavaScript
class MyComponent extends React.PureComponent {
  render() {}
}
```

2. `useCallback` & `useMemo`

```JavaScript
const memoizedCallback = useCallback(() => doSomething(a, b), [a, b]);
const memoizedValue = useMemo(() => computedValue(a, b), [a, b]);
```

3. 避免在渲染方法或函数组件中直接创建新的对象、数组或函数，否则其会在每次渲染时创建新的引用，即便数据未发生更改，从而导致子组件接收到新的 props 并重新渲染

```JavaScript
render() {
  const style = { color: 'red' };
  const handleClick = () => console.log('clicked');
  return <div style={style} onClick={handleClick}>Click me</div>;
}
```

4. 组件懒加载


