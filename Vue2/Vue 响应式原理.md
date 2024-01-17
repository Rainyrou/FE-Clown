![[Pasted image 20231031190525.png]]

###### 底层原理

- Vue2  响应式的特点是依赖收集，数据可变，自动派发更新，初始化时通过 `Object.defineProperty`  递归劫持  `data` 所有属性，为其添加 `getter` 和 `setter`。每个组件实例关联一个 `watcher` 实例，在组件渲染时进行依赖收集，并在依赖项的 `setter` 被调用时通知 `watcher` 重新渲染
- Vue3 使用原生 `Proxy`  重构响应式，`Proxy`  修复 Vue2 响应式存在的缺陷，它可以拦截对象的任意操作（包括增删对象属性、数组索引赋值和修改数组长度等），支持更多数据结构，且不再一开始就递归劫持对象属性，而是代理第一层对象本身，即包装响应式数据，运行时才递归，按需代理，用 `effect`  副作用函数来代替 Vue2  的 `watcher`，首次执行 `effect`，Vue 会访问响应式数据的属性，触发 `Proxy.get`，`effect` 被放到一个依赖集合中（Tracking Changes），当修改响应式数据的属性时，触发 `Proxy.set`，检索依赖集合并执行其中的 `effect`，自动追踪数据变化，用 `trackMap`  代替 Vue2  的 `Dep` 统一管理依赖，无需再维护大量依赖关系，性能显著提升
- React  的响应式不是自动的，它基于状态，单向数据流，数据不可变，需要手动 `setState`  来显式触发更新，且当数据改变时会以根组件为目录，默认重新渲染整个组件树，更新粒度更大一些，需要额外使用 `pureComponent` 和 `shouldComponentUpdate` 等防止不必要的渲染，保证整个组件树的渲染完全由其组件的 `props` 决定。若不符合上述情况，此类优化可能导致渲染结果不一致，说白了，React 的组件优化伴随着一定程度的心智负担。React16 之后引入 Fiber 架构，性能显著提升
- 总结：Vue 的响应式数据绑定提供了一种更加细粒度的更新机制，而 React 的函数式状态管理则倾向于更可预测的数据流

当你在异步更新队列中修改数据时，视图是不会立即更新的，Vue 将更新任务放入异步队列中，并在下一个事件循环周期中批量处理，以减少 DOM 操作次数

Vue2 用 `Vue.set` 和 `Vue.delete` 增删对象属性

#### 手写简易响应式原理

```JavaScript
const Observer = function (data) {
  for (let key in data) {
    defineReactive(data, key, data[key]);
  }
};

const defineReactive = function (obj, key, val) {
  if (typeof val === "object" && val !== null) {
    new Observer(val);
  }
  const dep = new Dep();
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get() {
      dep.depend();
      return val;
    },
    set(newVal) {
      if (newVal === val) return;
      val = newVal;
      dep.notify();
    },
  });
};

const observe = function (data) {
  return new Observer(data);
};

const Vue = function (options) {
  const self = this;
  if (options && typeof options.data === "function") {
    this._data = options.data.apply(this);
  }
  this.mount = function () {
    new Watcher(self, self.render);
  };
  this.render = function () {
    console.log("render: ", self._data.text);
  };
  observe(this._data);
};

const Watcher = function (vm, fn) {
  const self = this;
  Dep.target = this;
  this.addDep = function (dep) {
    dep.addSub(self);
  };
  this.update = function () {
    fn();
  };
  this.value = fn();
  Dep.target = null;
};

const Dep = function () {
  const self = this;
  this.subs = [];
  this.depend = function () {
    if (Dep.target) {
      Dep.target.addDep(self);
    }
  };
  this.addSub = function (watcher) {
    self.subs.push(watcher);
  };
  this.notify = function () {
    for (let i = 0; i < self.subs.length; i += 1) {
      self.subs[i].update();
    }
  };
};

const vue = new Vue({
  data() {
    return {
      text: "hello world",
    };
  },
});

vue.mount(); // render: hello world
vue._data.text = "123"; // render: 123
```

- `Observer` 负责将传入的对象转换为响应式对象，递归劫持  `data` 所有属性，为其添加 `getter` 和 `setter`
- `dep.depend()` 用于收集该属性与任何使用它的 `Watcher` 实例之间的依赖关系
- `observe` 负责创建 `Observer` 实例。如果传入的值是对象，它会被转换为响应式对象
- `Watcher` 负责将数据变化转发到外界
- `Dep` 统一管理依赖，进行依赖收集和触发响应，它是一个观察者模式 + 数据劫持的组合体，上游对接 `Observer`，下游对接 `Watcher`

##### Vue 类

- 在构造函数中，它初始化数据并调用 `observe` 来监听数据
- `mount` 用于挂载组件，它创建了一个 `Watcher` 实例，设置渲染函数与数据之间的响应关系
- `render` 渲染函数用于访问数据属性，这里只打印了 `text` 的值

##### Watcher 类

- 为组件渲染函数与数据之间建立联系，用于响应数据属性的更改

* 在构造函数中，它首先将自身赋值给全局的 `Dep.target`。这样当我们在渲染函数中访问数据时，数据的 `getter` 会知道有一个当前活跃的 `watcher`
* 执行渲染函数，访问数据的 `getter`，进行依赖收集
* 清除全局的 `Dep.target`

##### Dep 类

- 收集依赖并在数据改变时触发响应

* 使用一个数组 `subs` 来存储所有 `watcher`
* `depend` 用于收集依赖，将当前的 `watcher` 添加到 `subs` 中
* `notify` 用于触发响应，它会遍历 `subs` 中的每个 `watcher`，并调用它们的 `update` 方法

###### Dep.target

`Dep.target` 是一个全局的静态属性，用于存放当前活动（正在读取数据）的 `Watcher` 对象。当一个 `Watcher` 对象被创建时，它会被赋值到 `Dep.target`。在 `Watcher` 执行完后，`Dep.target` 会被设置为 `null`

当我们访问响应式对象的某个属性时，我们实际上是在执行该属性的 `getter` 函数。在 `getter` 函数中，会调用 `dep.depend()` 来将`Dep.target`（即当前活动的 `Watcher`）添加到 `dep` 的订阅列表中。这样，当属性值改变时，该 `Watcher` 会被通知并执行相应的操作（如重新渲染视图）

`Dep.target` 的主要目的是暂时保存当前的 `Watcher` 对象，以便 `Dep` 对象可以访问它

由于同一时间只能有一个 `Watcher` 被计算（即同时只能有一个 `Watcher` 访问响应式数据），因此只需要一个全局的位置来存放当前活动的 `Watcher`
 