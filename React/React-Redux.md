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

[Tutorial: Connect API | React Redux](https://react-redux.js.org/tutorials/connect)

`Provider`：用于包裹根组件，注入 Redux 的 `store`
`connect`：高阶组件，第一阶接收两个可选参数，`mapStateToProps` 接收整个 store `state`，返回该组件所需的对象即自定义 `state` 中哪些属性 connect 该组件，`mapDispatchToProps` 若为函数，其在组件创建时调用，接收 `dispatch` 参数，返回一个用于 `dispatch` 操作的对象，若为对象，每个 `action creater` 均作为 props，在调用 `mapDispatchToProps` 时自动 `dispatch action`。第二阶接收组件作为参数，将第一阶的参数注入到该组件中

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

![[Pasted image 20240505205745.png]]
