在引入 Fiber 架构前，React 使用堆栈渲染器的同步渲染引擎，一旦 React 开始渲染组件树即调用组件的 `render` 方法，它便会持续工作至完成整个组件树的渲染。此过程是同步的，一旦开始便不会被中断或暂停

1. 同步更新机制：旧的 React 更新机制是一个同步执行的过程，在这个过程中，React 递归遍历组件树，对需要更新的组件调用 `render` 方法，与前一次的渲染结果进行比对，最后更新 DOM
2. 阻塞主线程：此种不可中断的更新方式意味着较大的组件树复杂更新必然阻塞浏览器的主线程，影响用户体验
3. 缺乏任务优先级：由于更新过程是一次性完成的，React 旧的渲染机制无法为不同的更新任务分配不同的优先级

React Fiber 用于增强 React 的性能，尤其是在处理动画和页面渲染等方面，Fiber 实质上重构 React 虚拟 DOM，每个 React 元素对应一至多个 Fiber 节点，它们构成一个工作单元的树形结构，React 为每个 Fiber 节点标记 Effect，以表示不同类型的工作，每个 Fiber 节点代表一个工作单元，React 利用浏览器的空闲时间，通过 `requestIdleCallback` API 实现任务的分割和优先级排序，并依照优先级顺序执行这些工作单元，它使 React 可在渲染树的更新过程中中断和复用渲染任务，增强 React 渲染的可预测性。React 在渲染时构建两棵 Fiber 树，一棵为在屏幕上显示的 current tree，另一棵为正在内存中构建的工作树 work-in-progress tree。这种方式使 React 可在内存中构建新树，并在完成后进行快速切换。此外，渲染过程分为两个阶段，在协调阶段，React 异步计算新树变化，此过程可被中断，在提交阶段，React 遍历 Effect 列表，将计算出的变化同步应用到 DOM 上，此过程不可中断

- 时间分片：Fiber 将渲染任务分割成多个时间片段，允许中断和复用，避免阻塞主线程
- 优先级分配：Fiber 允许 React 为任务分配不同的优先级，确保优先处理关键的更新
- 错误处理：Fiber 引入新的错误边界概念，使组件能捕获并处理子组件树中的错误

React 调度过程是 Fiber 架构的一部分，其使 React 更好处理任务的优先级，提高应用的性能和响应性。React 的调度器 Scheduler 是其并发模式 Concurrent Mode 的核心，允许 React 在浏览器空闲时执行低优先级的任务，同时确保高优先级任务如用户输入及时响应，底层上利用浏览器的 `requestIdleCallback` API 来检测主线程的空闲时间，但由于 `requestIdleCallback` 的支持度和触发时机存在局限性，React 实现了自己的调度策略，其调度器内部维护一个任务队列，每个任务均有自己的到期时间，React 根据到期时间排序这些任务，尽可能在到期前执行它们。当浏览器主线程空闲时，React 检查队列中的任务，挑选出优先级最高的任务执行，而对于紧急任务，React 同样实现一个同步的调度模式，即使在忙碌的主线程上也会立即执行这些任务，确保关键任务如用户输入响应的流畅

1. 任务优先级：React 根据任务类型赋予不同的优先级，如用户交互和动画等被视为高优先级任务，而数据预加载等则为低优先级任务
2. 任务调度：React 调度器根据任务的优先级决定执行顺序，高优先级任务优先执行，而低优先级任务则在浏览器空闲时执行
3. 任务中断和恢复：在执行低优先级任务时，若出现更高优先级的任务如用户点击，React 中断当前任务，执行高优先级任务，待高优先级任务处理完毕再恢复先前的任务

```jsx
import React, { useState, useEffect } from 'react';

function MyComponent() {
  const [data, setData] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      const response = await fetch('https://api.example.com/data');
      const result = await response.json();
      setData(result);
    };
    fetchData(); // 浏览器空闲时请求数据
  }, []);

  return (
    <div>
      {data ? <div>{data}</div> : <div>Loading...</div>}
    </div>
  );
}
```
