```
// Template & JSX
// Vux/Pinia/Context/Redux
// Vue CLI/Vite/Create React App
// 组件通信
// 生命周期
// 性能优化
// 响应式 + Diff
```

Vue.js & React.js：JavaScript 库

1. **Template & JSX**

- Vue 通过基于 HTML 的模板语法提供数据驱动的视图层框架，允许我们声明式地将 DOM 绑定到底层 Vue 实例数据
* JSX 为 React 中类似 XML 的语法扩展，使得在 JavaScript 代码中可编写类似 HTML 的代码，更接近函数式编程，其提供简单直观的方式以声明 UI（语法糖），通过 Babel 转换为 JavaScript，通过 `React.createElement` 生成虚拟 DOM，通过 `ReactDOM.render` 将虚拟 DOM 转换为真实 DOM 并插入到页面中，其中 Fiber 架构调度和管理虚拟 DOM 更新，Diff 算法比较新旧虚拟 DOM 树中节点的差异并根据差异对真实 DOM 进行最小量更新

2. **响应式**：

- Vue2  在初始化组件时通过 `Object.defineProperty`  递归遍历 `data` 所有属性并为其添加 `getter` 和 `setter`，每个组件实例均关联一个 `watcher` 实例，其用于追踪组件的渲染依赖，当访问属性时触发 `getter` 收集依赖即将当前组件的 `watcher`  实例添加至对应 `dep`  实例中，当修改属性时触发 `setter` 通知对应 `dep` 实例的 `watcher`  实例执行更新操作，重新渲染组件，`Object.defineProperty` 无法直接监听增删对象属性、通过数组索引赋值和修改数组长度等操作，因此 Vue2 通过  `$set`  和 `$delete`  方法增删对象属性，通过重写数组变异方法 `push` + `pop` + `unshift` + `shift` + `sort` + `reverse` + `splice` 手动触发依赖更新，通过原型链劫持继承原生方法，在重写逻辑中注入依赖通知以保证原功能不丢失、响应式不失效，只重写数组变异方法，不重写非变异方法是因为原数组不变，无需触发依赖更新 + 若需使用新数组结果，赋值操作触发 setter 触发更新
- Vue3 重构响应式，通过 `Proxy` 修复 Vue2 响应式存在的缺陷，在运行时动态代理完整对象，拦截对象的所有属性操作而无需逐个劫持，按需递归即在访问嵌套对象时才递归创建代理，通过 `WeakMap`  和 `Set` 存储依赖关系，避免重复收集和内存泄漏，读取响应式数据时触发 `track`  将当前副作用函数收集到依赖集合中，修改响应式数据时触发 `trigger`，查找并执行依赖于该属性的所有副作用函数

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

- React  响应式基于 State 和 Props，侧重于单向数据流和不可变数据，手动 `setState`  显式触发更新，更新粒度为组件级别，默认重新渲染完整组件树，通过 `pureComponent` 和 `shouldComponentUpdate` 等方式进行性能优化
* Fiber 架构

3. **Diff 算法**：

- Vue2 Diff 算法采用双指针比较新旧虚拟节点的子节点列表，同层级 vnode 比较，若新的 vnode 存在而旧的不存在则创建新节点，反之则删除旧节点。在处理子节点时通过新前、新后、旧前和旧后四个指针比较，复用现有节点，避免不必要的 DOM 操作

头头比较（oldStartVnode 与 newStartVnode）：若相同，则指针均往后移
尾尾比较（oldEndVnode 与 newEndVnode）：若相同，则指针均往前移
头尾比较（oldStartVnode 与 newEndVnode）：若相同，表明旧头节点移动至尾部，相应指针调整
尾头比较（oldEndVnode 与 newStartVnode）：若相同，表明旧尾节点移动至头部，相应指针调整

以上步骤重复进行直至新旧节点的开始或结束指针交叉为止，再进行增删节点等操作

- Vue3 优化 Diff 算法，编译器在编译阶段检测并标记静态根节点，其及子树在后续更新过程中直接跳过，此外通过 Map 跟踪动态子节点索引，引入最长递增子序列算法优化含有 `v-for` 指令的列表渲染，通过计算新旧虚拟节点的 LIS 判断无需移动的节点，避免不必要的 DOM 操作
* React 同层级 vnode 比较，若节点标签和属性相同，则递归比较子节点，否则直接替换完整节点；在组件树中，若组件类型相同，则递归比较并更新组件内差异，否则直接销毁旧组件、创建新组件并挂载到 DOM 树上；在列表中，通过 `key` 唯一标识各个节点以比较新旧 DOM 节点，若类型和 key 相同，则直接复用该节点，否则删除旧节点并创建新节点，其遍历新列表的节点，将已存在的节点按相对位置插入，未找到的节点则创建并插入 DOM 上
