```
// 设计哲学
// Template & JSX
// Vux/Pinia/Context/Redux
// Vue CLI/Vite/Create React App
// 组件通信
// 生命周期
// 性能优化
// 响应式 + Diff
```

**它们都只是同一时代下产品的不同解决方案，仅此而已，它们终将会被时代抛弃（就像俺呜呜）。精通框架没什么屁用，要跳脱出框架本身，否则到死也不知道自己是怎么个死法**

Vue.js 和 React 都是构建用户界面的 JavaScript 库

1. **设计哲学**：

Vue 和 React 的核心理念差异决定了它们的发展轨迹。React 最初致力于制定 UI 开发的新范式，要求开发者按照一定规则来编码，React 敢做这样的尝试，是因为它有 Meta 的背书，保障它的流行和生态发展。而 Vue 聚焦于降低前端开发的门槛，更多地尊重和适应开发者的习惯，这也是它得以推广的一大因素

2. **Template & JSX**

- Vue 采用基于 HTML 的模板语法，提供数据驱动的视图层框架，允许开发者声明式地将 DOM 绑定到底层 Vue 实例的数据。Vue 更倾向于"万物皆组件"的思想，鼓励开发者使用组件来构建应用程序
* React 采用 JSX，使得在 JavaScript 代码中可以编写类似 HTML 的代码，更接近于函数式编程。React 更倾向于"学习一次，随处编写"的思想，侧重于 UI 组件化，采用单向数据流，通过 State 来控制 UI 更新。它不提供完整的框架解决方案，侧重于构建高效的 UI 层并鼓励使用不可变数据来推动 UI 的更新

3. **响应式**：

- Vue2  响应式的特点是依赖收集，数据可变，自动派发更新，初始化时通过 `Object.defineProperty`  递归劫持  `data` 所有属性，为其添加 `getter` 和 `setter`，每个组件实例关联一个 `watcher` 实例，`watcher`  实例用于追踪组件的渲染依赖，访问属性时触发  `getter` 将当前组件的  `watcher`  实例添加到 `dep`  对象中，而  `dep`  对象直接存储于响应式对象，修改属性时触发  `setter` 通知当前组件的  `watcher`  实例更新，重新渲染组件，当组件销毁时 Vue2 手动清理所有依赖关系以避免内存泄漏，但 `Object.defineProperty` 无法直接监听增删对象属性、数组索引赋值和修改数组长度等操作，因此 Vue2 通过  `$set`  和  `$delete`  方法增删对象属性，通过重写数组的变异方法手动触发依赖更新
- Vue3 重构响应式，通过 `Proxy` 修复 Vue2 响应式存在的缺陷，支持监听增删对象属性，通过数组索引赋值和修改数组长度，Vue3 只在运行时拦截数据，使用 `Proxy` 动态代理对象属性访问，不再在初始化时递归遍历，只有在访问嵌套对象时才递归创建代理，使用 `WeakMap` 和 `Set` 高效存储依赖关系以避免重复收集及内存泄漏，读取响应式数据时触发 `track` 函数将当前副作用函数收集到依赖集合中，修改响应式数据时触发 `trigger`，查找并执行依赖于该属性的所有副作用函数

`Proxy` 可拦截以下类型的操作：

1. 定义属性 `Object.defineProperty`
2. 获取属性 `get`
3. 设置属性 `set`
4. 删除属性 `deleteProperty`
5. 检查属性 `has`
6. 获取对象的自有属性键 `Object.getOwnPropertyNames`
7. 获取对象的属性描述符 `Object.getOwnPropertyDescriptor`
8. 获取原型 `Object.getPrototypeOf`
9. 设置原型 `Object.setPrototypeOf`
10. 调用函数 `apply`

```js
const targetMap = new WeakMap();
let activeEffect = null;

function track(target, key) {
  if (!activeEffect) return;
  let depsMap = targetMap.get(target);
  if (!depsMap) targetMap.set(target, (depsMap = new Map()));
  let dep = depsMap.get(key);
  if (!dep) depsMap.set(key, (dep = new Set()));
  dep.add(activeEffect);
}

function trigger(target, key) {
  const depsMap = targetMap.get(target);
  if (!depsMap) return;
  const dep = depsMap.get(key);
  if (dep) dep.forEach((effect) => effect());
}

function reactive(target) {
  return new Proxy(target, {
    get(target, key, receiver) {
      track(target, key);
      return Reflect.get(target, key, receiver);
    },
    set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver);
      trigger(target, key);
      return result;
    },
  });
}

function effect(fn) {
  activeEffect = fn;
  fn();
  activeEffect = null;
}

const state = reactive({ count: 66 });
effect(() => console.log(`count is: ${state.count}`));
state.count++;
```

- React  的响应式不是自动的，基于状态，单向数据流，数据不可变，需手动 `setState`  来显式触发更新，且当数据改变时以根组件为目录，默认重新渲染整个组件树，更新粒度更大一些，需额外使用 `pureComponent` 和 `shouldComponentUpdate` 等防止不必要的渲染，保证整个组件树的渲染完全由其组件的 `props` 决定，React 的组件优化伴随着一定程度的心智负担
* 在引入 Fiber 架构前，React 通过堆栈渲染器的同步渲染引擎来渲染组件，React 递归遍历组件树，对需更新的组件调用 `render` 方法，与前一次的渲染结果进行比对，更新 DOM，此种更新机制是同步且一次性完成的，一旦开始便不被中断或暂停，其无法为不同渲染任务分配不同优先级且较大组件树的复杂更新势必阻塞浏览器主线程
* React Fiber 正是为解决上述问题而生的，其用于提高 React 的性能，尤其是在处理动画和页面渲染等方面，Fiber 实质上重构 React Virtual DOM，每个 React 元素对应一至多个 Fiber 节点，其构成一个工作单元的树形结构，React 为每个 Fiber 节点标记 Effect 来表示不同类型的工作，每个 Fiber 节点代表一个工作单元，React 调度过程为 Fiber 架构的一部分，调度器 Scheduler 是其并发模式 Concurrent Mode 之核心，React 在底层上通过类似 `requestIdleCallback` 的机制检测浏览器主线程的空闲时间，实现任务分割和优先级排序，并据此顺序执行这些 Fiber 节点，具体来说，由于 `requestIdleCallback` 的支持和触发时机存在局限性，React 实现自己的调度策略，其调度器内部维护一个任务队列，每个任务均有其到期时间，React 据此排序它们，在浏览器空闲时执行高优先级任务，在更新过程中中断和复用渲染任务，避免阻塞浏览器主线程，确保优先处理关键更新，增强 React 渲染的可预测性。React 在渲染组件时构建两棵 Fiber 树，一棵为在屏幕上显示的 current tree，另一棵为正在内存中构建的工作树 work-in-progress tree，此种方式使 React 可在内存中构建新树并在完成后进行快速切换，在协调阶段，React 异步计算新树变化（此过程可被中断），在提交阶段，React 遍历 Effect 列表，将计算出的变化同步应用到 DOM 上（此过程不可中断）

4. **Diff 算法**：

- 虚拟 DOM 为一个树形结构的 JavaScript 对象，Vue Virtual DOM 实现参考 `snabbdom.js` 和 `inferno.js`，其为解决浏览器性能问题而生，核心为在 JavaScript 模拟 DOM 树，状态更新反映在虚拟 DOM 上，操作内存中的 JS 对象显然更快，等批量更新后，将虚拟 DOM 映射为真实 DOM，再由浏览器渲染，通过 Diff 算法比较新旧虚拟 DOM 树中节点的差异并根据差异对真实 DOM 进行最小量更新。虚拟 DOM 抽象浏览器渲染过程，不再局限于浏览器 DOM，实现跨平台能力
- Vue2 的 Diff 算法采用双指针比较新旧虚拟节点的子节点列表，同层级 vnode 比较，若新的 vnode 存在而旧的不存在则创建新节点，反之则删除旧节点。在处理子节点时维护新前、新后、旧前和旧后四个指针进行比较，尽可能复用现有节点，避免不必要的 DOM 操作

1. 头头比较（oldStartVnode 与 newStartVnode）：如果相同，则指针均往后移
2. 尾尾比较（oldEndVnode 与 newEndVnode）：如果相同，则指针均往前移
3. 头尾比较（oldStartVnode 与 newEndVnode）：如果相同，表明旧头节点移动至尾部，相应指针调整
4. 尾头比较（oldEndVnode 与 newStartVnode）：如果相同，表明旧尾节点移动至头部，相应指针调整

以上步骤循环进行直至新旧节点的开始或结束指针交叉，而后若新旧节点列表长度不同，则增删相应节点

- Vue3 优化 Diff 算法，增加更多静态标记，编译器在编译阶段检测并标记静态根节点，其及子树在后续更新过程中被直接跳过，此外通过 Map 跟踪动态子节点索引，引入最长递增子序列算法优化含有 `v-for` 指令的列表渲染，通过计算新旧虚拟节点的 LIS 确定哪些元素无需移动，减少重绘回流的开销
* React 同层级 vnode 比较，若节点标签相同则比较节点属性及子节点，否则直接替换整个节点，在组件树中，若组件类型相同则递归对比并更新组件内差异，否则直接销毁旧组件、创建新组件并挂载到 DOM 树上，在列表中，通过 `key` 唯一标识各个节点以判断新旧 DOM 树的节点是否为同一节点，若类型和 key 相同则直接复用该节点，否则删除旧节点并创建新节点，其遍历新列表的节点，将已存在的节点按相对位置插入，未找到的节点则创建并插入 DOM 上

