`forwardRef` 允许组件使用 ref 将 DOM 节点暴露给父组件

```JSX
const Child = forwardRef((props, ref) => <input ref={ref} />);

const Parent = () => {
  const inputRef = useRef();
  useEffect(() => inputRef.current.focus(), []);
  return <Child ref={inputRef} />;
};
```

- `Parent` 组件使用 `useRef` 钩子来创建一个 ref，并将其传递给 `Child` 组件，由于 `Child` 组件通过 `forwardRef` 接收这个 ref，并将其赋给其内部的 `input` 元素，因此 `inputRef` 实际上引用了 `Child` 组件内部的 `input` DOM 元素
- 这种模式允许父组件直接控制子组件内的 DOM 元素，而无需通过传统的 DOM 查询方法如`document.getElementById` 或 `querySelector` 等
