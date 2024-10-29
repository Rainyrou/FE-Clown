![[Pasted image 20240505205745.png]]

[Tutorial: Connect API | React Redux](https://react-redux.js.org/tutorials/connect)

1. React-Redux 连接 React 和 Redux 即允许组件直接通过 React 的 `useContext` 或 `connect` 高阶组件访问到 Redux 的 `store` 而无需通过 `props` 逐层传递，简化组件间的通信和数据共享
2. `Provider`：高阶组件，用于包裹根组件，注入 Redux 的 `store` 于 `context` 即所有子组件均能通过 `context` 访问到 Redux 的 `store`
3. `connect`：高阶组件，第一阶接收两个可选参数，`mapStateToProps` 接收整个 store `state`，返回该组件所需的对象即自定义 `state` 中哪些属性 connect 该组件，`mapDispatchToProps` 若为函数，其在组件创建时调用，接收 `dispatch` 参数，返回一个用于 `dispatch` 操作的对象，若为对象，每个 `action creater` 均作为 `props`，在调用 `mapDispatchToProps` 时自动 `dispatch action`。第二阶接收组件作为参数，将第一阶的参数注入到该组件中。`connect` 使用浅比较，根据传入的 `state` 中的自定义属性决定是否更新，使用 `useReducer` 和 `useLayoutEffect` 来优化组件更新
4. React-Redux 通过 `Subscription` 类自定义组件通信方式以解决父子组件各自依赖 Redux 的问题，只有连接 Redux 的根组件才直接注册到 Redux 的 `store` 中，其他组件注册到距离父组件最近的 `subscription` 实例中。 每个 `connect` 组件创建时均生成一个 `Subscription` 实例，用于监听 `store` 变化以决定组件是否需要更新，若需更新，`Subscription` 触发组件的重新渲染并将新的 `props` 传递给组件，同时通过 `notifyNestedSub` 方法通知其所有子组件的 `Subscription` 实例

`store.js`

```js
import { createStore } from "redux";

const initState = {
  count: 0,
};

function reducer(state = initState, action) {
  switch (action.type) {
    case "INCREMENT":
      return { ...state, count: state.count + 1 };
    case "DECREMENT":
      return { ...state, count: state.count - 1 };
    case "RESET":
      return { ...state, count: 0 };
    default:
      return state;
  }
}

const store = createStore(reducer);
export default store;
```

`App.js`

```js
import "./App.css";
import React from "react";
import { Provider, connect } from "react-redux";
import store from "./store";

function Counter(props) {
  const { count, incrementHandler, decrementHandler, resetHandler } = props;
  return (
    <>
      <h3>{count}</h3>
      <button onClick={incrementHandler}>+1</button>
      <button onClick={decrementHandler}>-1</button>
      <button onClick={resetHandler}>reset</button>
    </>
  );
}

const mapStateToProps = (state) => {
  return {
    count: state.count,
  };
};

const mapDispatchToProps = (dispatch) => ({
  incrementHandler: () => dispatch({ type: "INCREMENT" }),
  decrementHandler: () => dispatch({ type: "DECREMENT" }),
  resetHandler: () => dispatch({ type: "RESET" }),
});

const ConnectedCounter = connect(mapStateToProps, mapDispatchToProps)(Counter);

function App() {
  return (
    <Provider store={store}>
      <ConnectedCounter />
    </Provider>
  );
}

export default App;
```

`App.js`

```js
import React, { createContext, useContext } from "react";
import { Provider, connect } from "react-redux";
import store from "./store";

const CounterContext = createContext();

function Counter(props) {
  const { count } = props;
  const { incrementHandler, decrementHandler, resetHandler } =
    useContext(CounterContext);
  return (
    <>
      <h3>{count}</h3>
      <button onClick={incrementHandler}>+1</button>
      <button onClick={decrementHandler}>-1</button>
      <button onClick={resetHandler}>reset</button>
    </>
  );
}

const mapStateToProps = (state) => {
  return {
    count: state.count,
  };
};

const ConnectedCounter = connect(mapStateToProps)(Counter);

function App() {
  const contextValue = {
    incrementHandler: () => store.dispatch({ type: "INCREMENT" }),
    decrementHandler: () => store.dispatch({ type: "DECREMENT" }),
    resetHandler: () => store.dispatch({ type: "RESET" }),
  };

  return (
    <Provider store={store}>
      <CounterContext.Provider value={contextValue}>
        <ConnectedCounter />
      </CounterContext.Provider>
    </Provider>
  );
}

export default App;
```

