在 Hooks 中直接调用子组件中的函数并不像在类组件中那样直接，由于函数组件的特性，我们不能通过 `ref` 属性直接调用子组件的函数，因为 `ref` 不能直接绑定到函数组件上，除非子组件是一个类组件，但还是可通过`useImperativeHandle` & `forwardRef` 实现。虽然此种方式可实现上述需求，但 React 官方建议避免过度使用 refs 来实现组件间的交互，因为这可能破坏组件间的独立性和封装性，推荐通过状态提升或使用上下文 Context 实现

`useImperativeHandle` 允许子组件向父组件暴露特定的实例值，而 `forwardRef` 允许将 `ref` 从父组件传递给子组件

```jsx
import React, { useImperativeHandle, forwardRef } from 'react';

const Child = forwardRef((props, ref) => {
  useImperativeHandle(ref, () => ({
    showMessage() {
      alert('Hello from Child Component!');
    }
  }));

  return <div>Child Component</div>;
});
```

```jsx
import React, { useRef } from 'react';
import Child from './Child';

function Parent() {
  const childRef = useRef();

  return (
    <div>
      <Child ref={childRef} />
      <button onClick={() => childRef.current.showMessage()}>
        Call Child Method
      </button>
    </div>
  );
}
```
