React 渲染阶段：

React 组件渲染为 JSX → JavaScript → 虚拟 DOM → 真实 DOM，基于 Fiber 架构拆分为调度 → 协调 → 提交

1. 调度阶段：

- 优先级量化：React 17 从基于时间戳量化的 expirationTime 模型转向基于位运算量化的 Lanes 模型，通过二进制位运算掩码实现将不同优先级的渲染任务映射至不同二进制位（车道），优先级依次为 Immediate -> UserBlocking -> Normal -> Low -> Idle
- 任务排序：调度器基于最小堆和 Lanes 优先级排序更新任务队列，确保每次获取优先级最高的任务
- 时间切片：通过 `MessageChannel`  实现时间切片，每次只执行单个 Fiber 节点的计算，剩余任务置于下一时间分片，若高优先级任务进入任务队列，调度器立即中断当前执行的低优先级任务（通过 current tree + workInProgress tree 保存执行状态，记录当前节点指针），切换至高优先级任务执行
- 任务恢复：被中断的低优先级任务重新进入队列，待高优先级任务执行完毕后恢复（从记录的节点指针继续遍历）；若长时间未执行低优先级任务，则自动升级其优先级

2. 协调阶段：

- 构建 Fiber 树：各个 React 元素对应一至多个 Fiber 节点，Fiber 节点包含父子兄弟指针，将递归遍历转换为迭代遍历，React 为各个 Fiber 节点标记 Effect 类型并拼接为副作用链表；基于双缓冲机制，从 Fiber 根节点开始逐节点构建 workInProgress 树
- Diff 算法：React 比较同层级 vnode，若节点类型（DOM 标签或组件类型）不同，直接销毁旧节点、创建新节点，再通过 `key` 唯一标识比较节点，若 `key` 不同则销毁旧节点、创建新节点，否则复用该节点，递归比较其属性和子节点，已存在的节点根据相对位置移动
- 通过 Fiber 架构的双缓冲渲染模型（current tree + workInProgress tree） + Map 存储 vnode 和 `key` + 副作用列表存储 DOM 的增删修改操作，因此 React Diff 的时间和空间复杂度均为 $O(N)$

3. 提交阶段

- beforeMutation 阶段执行 DOM 操作前的准备工作如读取 DOM 节点的布局信息和调用`getSnapshotBeforeUpdate/useLayoutEffect`
- mutation 阶段遍历副作用链表，根据 Fiber 节点的 Effect 标记执行相应的 DOM 操作
- layout 阶段执行 DOM 操作后的收尾工作如更新 Fiber 树的引用，将 workInProgress tree 切换为 current tree、调用  `componentDidMount/componentDidUpdate/useLayoutEffect`
* 卸载阶段：移除真实 DOM 节点 + 副作用 + 触发 `componentWillUnmount/useEffect` 清理函数

Fiber：

- 优先级量化：React 17 从基于时间戳量化的 expirationTime 模型转向基于位运算量化的 Lanes 模型，通过二进制位运算掩码实现将不同优先级的渲染任务映射至不同二进制位（车道），优先级依次为 Immediate -> UserBlocking -> Normal -> Low -> Idle
- 任务排序：调度器基于最小堆和 Lanes 优先级排序更新任务队列，确保每次获取优先级最高的任务
- 时间切片：通过 `MessageChannel`  实现时间切片，每次只执行单个 Fiber 节点的计算，剩余任务置于下一时间分片，若高优先级任务进入任务队列，调度器立即中断当前执行的低优先级任务（通过 current tree + workInProgress tree 保存执行状态，记录当前节点指针），切换至高优先级任务执行
- 任务恢复：被中断的低优先级任务重新进入队列，待高优先级任务执行完毕后恢复（从记录的节点指针继续遍历）；若长时间未执行低优先级任务，则自动升级其优先级
- 构建 Fiber 树：各个 React 元素对应一至多个 Fiber 节点，Fiber 节点包含父子兄弟指针，将递归遍历转换为迭代遍历，React 为各个 Fiber 节点标记 Effect 类型并拼接为副作用链表；基于双缓冲机制，从 Fiber 根节点开始逐节点构建 workInProgress 树

* 首次渲染 `App → A → B → C → D` → `useEffect` 执行 → `state = 1` → 重新渲染 `App → A → B → C → D`
* `console.log` 属于渲染阶段，useEffect 属于提交阶段，因此副作用逻辑晚于 App/A/B/C/D 的打印

```js
function A() {
  console.log("A");
  return <B />;
}

function B() {
  console.log("B");
  return <C />;
}

function C() {
  console.log("C");
  return null;
}

function D() {
  console.log("D");
  return null;
}

function App() {
  const [state, setState] = useState(0);
  useEffect(() => setState((state) => state + 1), []);
  console.log("App");
  return (
    <div>
      <A state={state} />
      <D />
    </div>
  );
}

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```
