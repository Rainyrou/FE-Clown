`useState` 的 `set` 方法被认为是异步的，尽管它并不符合传统意义上的异步操作。`setState` 调用并不会立即更新状态，而是触发一个状态更新的过程。当你调用 `setState` 时，React 会将新的状态值排入更新队列，并计划在稍后的某个时间点更新组件状态，我们不能立即在调用 `setState` 后访问更新后的状态，因为这个操作在 React 的下一个渲染周期中完成。这种设计是为了提高性能和保证组件行为的一致性，若每次 `setState` 调用均立即更新状态，那么在处理频繁的状态更新时，应用性能将受到严重影响。此外这种批处理方式也使得多个 `setState` 调用可以合并，从而减少不必要的渲染次数

1. 异步函数 & `await` ：将 `setState` 放在异步函数中，在每次 `setState` 后使用 `await` 与 `setTimeout` 组合来暂停，以模拟同步的 `setState` 行为，此种方法依赖于 JavaScript 事件循环

```jsx
class MyComponent extends React.Component {
  state = { count: 0 };

  updateStateMultipleTimes = async () => {
    await this.setStateAsync({ count: this.state.count + 1 });
    console.log(this.state.count); 
    await this.setStateAsync({ count: this.state.count + 1 });
    console.log(this.state.count); 
  };

  setStateAsync(state) {
    return new Promise(resolve => {
      this.setState(state, resolve);
    });
  }

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.updateStateMultipleTimes}>Increment Twice</button>
      </div>
    );
  }
}
```