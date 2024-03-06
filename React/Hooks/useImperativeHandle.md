```JSX
const Child = forwardRef((props, ref) => {
  useImperativeHandle(ref, () => ({
    showAlert() {
      alert("Alert from Child");
    },
  }));
  return <div>Hello</div>;
});

const Parent = () => {
  const childRef = useRef();
  return (
    <>
      <Child ref={childRef} />
      <button onClick={() => childRef.current.showAlert()}>Show Alert</button>
    </>
  );
};
```

`useImperativeHandle` 允许自定义父组件通过 ref 可以访问的子组件的实例值，它允许你在使用 ref 时，让父组件访问子组件中特定的方法或属性，而不是子组件的整个实例
