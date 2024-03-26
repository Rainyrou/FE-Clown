1. React Context：提供一种在组件树中共享值如用户登陆状态认证、明暗主题或国际化等，而无需显式通过组件树逐层传递 props

- 简单直接，无需引入额外的库
- 局部状态共享

```jsx
import React, { useContext } from 'react';

const ThemeContext = React.createContext('light');

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button theme={theme}>I am styled by theme context!</button>;
}
```

2. Redux

- 支持使用中间件来扩展 Redux 的功能
- 大量全局状态管理 + 复杂的数据流和多个数据源
- 中心化管理，提供单一真实数据源
- 可预测性，通过纯函数 Reducer 来管理状态的更新
- Redux DevTools 支持时间旅行特性

```jsx
import { createStore } from 'redux';

// Action
const increment = () => ({ type: 'INCREMENT' });
const decrement = () => ({ type: 'DECREMENT' });

// Reducer
const counter = (state = 0, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
    default:
      return state;
  }
};

// Store
const store = createStore(counter);

// Dispatch
store.dispatch(increment()); // 1
store.dispatch(increment()); // 2
store.dispatch(decrement()); // 1

// Subscribe
store.subscribe(() => console.log(store.getState()));
```
