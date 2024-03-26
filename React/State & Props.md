State 允许组件保存、更新和使用数据，无论是类组件还是函数组件，React 的状态管理都基于其重新渲染机制。当组件的状态改变时，React 重新渲染该组件及其子组件，以反映最新状态，React 在底层维护一个状态变更队列，确保状态更新异步且高效处理

1. 类组件中的状态管理:

   - 在类组件的 `constructor` 中初始化状态
   - 通过 `this.setState()` 方法更新组件状态，该方法是异步的，且在状态更新后重新渲染组件
   - 通过 `this.state` 访问当前组件状态

```TSX
class MyComponent extends React.Component {
    constructor(props) {
        super(props);
        this.state = { count: 0 };
    }

    increment = () => {
        this.setState({ count: this.state.count + 1 });
    }

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

2. State Lifting，React 16.8 引入 Hooks，如 `useState`

   - 初始化状态：使用 `useState` Hook 在函数组件中添加状态
   - 更新状态: `useState` 返回一个数组，其中第一个元素是状态变量，第二个元素是更新该状态的函数
   - 访问状态: 直接使用状态变量

```TSX
import React, { useState } from 'react';

function MyComponent() {
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

Props：从父组件传递到子组件的数据，只读，子组件不能修改接收到的 Props，它是外部的，在创建组件时提供的，并在整个组件生命周期中保持不变
