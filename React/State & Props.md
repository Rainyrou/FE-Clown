State 允许组件访问和更新数据，React 的状态管理基于其重新渲染机制，当组件的状态变化时，React 重新渲染该组件及其子组件以反映最新状态，React 在底层维护一个状态队列以确保状态更新异步且高效处理；Props 是父组件传递给子组件的只读数据，子组件无法修改 Props，Props 在组件初始化时提供的并在整个组件生命周期中保持不变

1. 类组件：在 `constructor` 中初始化状态，通过 `this.state` 访问当前组件状态，通过 `this.setState` 异步更新当前组件状态并在状态更新后重新渲染组件

```jsx
class MyComponent {
  constructor(props) {
    super(props);
    this.state = { count: 1 };
  }

  increment = () => this.setState({ count: this.count + 1 });

  render() {
    return (
      <div>
        <p>{this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}
```

2. 函数组件：通过 `useState` 初始化状态，通过 `xxx` 访问当前组件状态，通过 `setXxx` 异步更新当前组件状态并在状态更新后重新渲染组件

```jsx
import { useState } from "react";

export function MyComponent() {
  const [count, setCount] = useState(0);
  const increment = () => setCount(count + 1);

  return (
    <div>
      <p>{count}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```
