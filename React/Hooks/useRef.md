`useRef` 用于在函数组件内创建一个 ref，该 ref 可持久化整个组件的生命周期。`useRef` 用于访问 DOM 元素、存储上一个状态的值或任何可变值，该值不会引起组件的重新渲染。它返回的对象有一个 `.current` 属性，用于访问其持有的可变值

```JSX
const myComponent = () => {
  const myRef = useRef(null);
  useEffect(() => console.log(myRef.current), []);
  return <div ref={myRef}>Hello</div>;
};
```
