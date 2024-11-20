Redux 通过 `createStore(reducer)` 创建全局 Store 以存储全局状态，通过 `reducer` 的默认参数或显式传递的 `initialState` 设置初始 State，`Provider` 注入 Redux 的 `store` 于 `context`，React-Redux 连接 React 和 Redux 即允许组件直接通过 React 的 `useSelector` 和 `connect` 高阶组件访问到 Redux 的 `store`，通过 `dispatch` 触发 `action` 用于状态更新 ，`store` 接收到 `action` 后将其传递给 `reducer`，`reducer` 根据当前 State 和 `action` 计算出新的 State，若存在多个 `reducer`，Redux 调用 `combineReducers` 将它们组合起来，各个 `reducer` 只负责管理自己的状态切片，Redux 将状态拆分并分别传入对应的子 `reducer`，再将所有子状态合并为完整的状态树，Redux 中间件位于 `dispatch` 和 `reducer` 间，拦截 `action`，用于添加日志记录、错误监控和异步逻辑等如 `redux-thunk` 允许 `action` 为处理异步逻辑的函数，`redux-saga` 基于生成器函数管理副作用，而状态更新后，Redux 调用通过 `store.subscribe(listener)` 注册的监听器通知状态变化，React-Redux 的 `connect` 或 `useSelector` 监听 `store` 变化并重新渲染相应组件

Redux 只是一个单纯的状态管理库，没有 View 层

- `createStore`：用于创建 `store` 存储状态，接收 `reducer` 和 `enhancer`，`enhancer` 可选，其为装饰器模式，接收当前的 `createStore`，返回增强版的 `createStore`

* `store.getState`：用于访问 `state`
* `store.dispatch(action)`：用于执行 `reducer`，更新 `state`
* `store.subscribe`：用于订阅 `state` 变化，当调用 `store.dispatch(action)` 时被触发
* `combineReducers`：用于合并多个 `reducer`，返回新的 `reducer`
* `applyMiddleware`：返回增强版的 `enhancer`，中间件为装饰器模式，接收当前的 `dispatch`，返回增强版的 `dispatch`

```bash
npx create-react-app redux-milk-example
cd redux-milk-example
npm i --save redux react-redux
```

`src/store.js`：

```js
import { createStore, combineReducers } from "redux";

const initMilkState = {
  milk: 0,
};

function milkReducer(state = initMilkState, action) {
  switch (action.type) {
    case "PUT_MILK":
      return { ...state, milk: state.milk + (action.count || 1) };
    case "TAKE_MILK":
      return { ...state, milk: state.milk - (action.count || 1) };
    default:
      return state;
  }
}

const initRiceState = {
  rice: 0,
};

function riceReducer(state = initRiceState, action) {
  switch (action.type) {
    case "PUT_RICE":
      return { ...state, rice: state.rice + (action.count || 1) };
    case "TAKE_RICE":
      return { ...state, rice: state.rice - (action.count || 1) };
    default:
      return state;
  }
}

const reducer = combineReducers({
  milkReducer: milkReducer,
  riceReducer: riceReducer,
});
const store = createStore(reducer);
export default store;
```

`App.js`

```js
import "./App.css";
import React from "react";
import { Provider, useDispatch, useSelector } from "react-redux";
import store from "./store";

function MilkorRiceApp() {
  const milk = useSelector((state) => state.milkReducer.milk);
  const rice = useSelector((state) => state.riceReducer.rice);
  const dispatch = useDispatch();
  const putMilk = () => dispatch({ type: "PUT_MILK", count: 1 });
  const takeMilk = () => dispatch({ type: "TAKE_MILK", count: 1 });
  const putRice = () => dispatch({ type: "PUT_RICE", count: 1 });
  const takeRice = () => dispatch({ type: "TAKE_RICE", count: 1 });
  return (
    <div>
      <h1>Milk in Store: {milk}</h1>
      <button onClick={putMilk}>Put Milk</button>
      <button onClick={takeMilk}>Take Milk</button>
      <h1>Rice in Store: {rice}</h1>
      <button onClick={putRice}>Put Rice</button>
      <button onClick={takeRice}>Take Rice</button>
    </div>
  );
}

function App() {
  return (
    <Provider store={store}>
      <MilkorRiceApp />
    </Provider>
  );
}

export default App;
```

```bash
npm start
```

手写 Redux

```js
function createStore(reducer, enhancer) {
  if (enhancer && typeof enhancer === "function") {
    const newCreateStore = enhancer(createStore);
    const newStore = newCreateStore(reducer);
    return newStore;
  }
  let state;
  let listeners = [];
  function getState() {
    return state;
  }
  function dispatch(action) {
    state = reducer(state, action);
    for (let i = 0; i < listeners.length; ++i) {
      const listener = listeners[i];
      listener();
    }
  }
  function subscribe(callback) {
    listeners.push(callback);
  }
  const store = {
    getState,
    dispatch,
    subscribe,
  };
  return store;
}

function combineReducers(reducersMap) {
  const reducerKeys = Object.keys(reducersMap);
  const reducer = (state = {}, action) => {
    const newState = {};
    for (let i = 0; i < reducerKeys.length; ++i) {
      const key = reducerKeys[i];
      const currentReducer = reducersMap[key];
      const preState = state[key];
      newState[key] = currentReducer(preState, action);
    }
    return newState;
  };
  return reducer;
}

function compose(...fns) {
  return fns.reduce(
    (a, b) =>
      (...args) =>
        a(b(...args))
  );
}

function applyMiddleware(...middlewares) {
  function enhancer(createStore) {
    function newCreateStore(reducer) {
      const store = createStore(reducer);
      const chain = middlewares.map((middleware) => middleware(store));
      const { dispatch } = store;
      const newDispatchGen = compose(...chain);
      const newDispatch = newDispatchGen(dispatch);
      return { ...store, dispatch: newDispatch };
    }
    return newCreateStore;
  }
  return enhancer;
}

export { createStore, combineReducers, applyMiddleware, compose };
```
