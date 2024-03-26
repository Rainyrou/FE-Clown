1. 当组件的状态逻辑复杂且包含多个子值，这些子值之间相互依赖：`useReducer` 允许将这些逻辑集中在一个地方处理，使得状态的更新可预测
2. 下一个状态依赖于之前的状态：`useReducer` 提供的 `reducer` 函数接收当前状态和一个动作对象，返回一个新的状态
3. 逻辑复用：只需将 `reducer` 函数和初始状态提取到外部文件，再在需要的组件中引用
4. 更新深层嵌套的对象的状态：在 `reducer` 函数中返回新的状态对象，而无需担心修改原始状态

```jsx
import React, { useReducer } from 'react';

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
    </>
  );
}
```


