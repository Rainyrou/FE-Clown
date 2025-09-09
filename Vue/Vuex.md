![[Pasted image 20231108200514.png]]

dispatch -> actions -> commit -> mutations -> state -> getters -> dispatch

Vuex 为全局状态管理库，通过 `Vue.use` 初始化 Vuex，底层通过调用 `install` 和 `mixin` 注入 `$store` 至所有组件实例，在组件的生命周期中将 `$store` 注入到组件实例，在组件中通过 `this.$store` 访问 Store。其为基于 Flux 架构的单一状态树，将状态树分割为 module，各个模块均有独立的 `state`、`getters`、`mutations` 和 `actions`，其被注册至全局命名空间，模块状态递归合并至 Store 全局状态树。Vuex 以单向数据流、响应式、可预测的方式确保状态变化，即通过 `dispatch` 触发  `actions` 执行异步操作再通过 `commit` 提交 `mutations` 同步修改 `state`

Pinia：Vuex 精神继承者，与 TypeScript 兼容， 轻量，压缩后仅有 1KB，结构扁平化，无模块嵌套，将状态树分割为 `store`，各个 `store` 均有独立的 `state`、`getters` 和 `actions`，移除 `mutations`，直接在 `actions` 中修改 `state` 且支持同步和异步操作

- State：Vuex 存储的核心，存储所有组件的共享状态，在底层上通过 `Vue.observable` 使其变为响应式，在组件中通过 `this.$store.state` 访问或通过 `mapState` 函数将 `store` 中多个 `state` 映射到组件的计算属性
- Getters：与 Vue `computed` 类似，用于获取全局 `state`，若依赖的 `state` 变化，`getters` 重新计算，触发依赖 `getters` 组件的重新渲染，在组件中通过 `this.$store.getters` 调用或通过 `mapGetters` 函数将 `store` 中的 `getters` 映射到组件的计算属性
- Mutations：接收参数为字符串类型的 `type` 事件和 `handler` 回调，在组件中通过 `this.$store.commit` 调用，当 `mutations` 提交时，Vuex 查找并执行相应 `mutations`
* Actions：接收参数为一个与 `store` 实例有相同属性和方法的 `context` 对象，或通过解构赋值直接获取 `state`、`getters`、`commit` 和 `dispatch`，在组件中通过 `this.$store.dispatch` 调用，返回一个 Promise 对象，支持链式调用即提交多个 `mutations`

```js
let _Vue = null;

class Store {
  constructor(options) {
    const state = options.state || {};
    const getters = options.getters || {};
    const mutations = options.mutations || {};
    const actions = options.actions || {};
    this.state = _Vue.observable(state);
    this.getters = Object.create(null);
    Object.keys(getters).forEach((key) =>
      Object.defineProperty(this.getters, key, {
        get: () => getters[key].call(this, this.state),
      })
    );
    this.mutations = {};
    Object.keys(mutations).forEach(
      (key) =>
        (this.mutations[key] = (params) =>
          mutations[key].call(this, this.state, params))
    );
    this.actions = {};
    Object.keys(actions).forEach(
      (key) =>
        (this.actions[key] = (params) => actions[key].call(this, this, params))
    );
  }
  commit = (eventName, params) => this.mutations[eventName](params);
  dispatch = (eventName, params) => this.actions[eventName](params);
}

function install(Vue) {
  _Vue = Vue;
  _Vue.mixin({
    beforeCreate() {
      if (this.$options.store) _Vue.prototype.$store = this.$options.store;
    },
  });
}

const mapState = (params) => {
  if (!Array.isArray(params))
    throw new Error("Sorry, this version supports the array parameters");
  let obj = {};
  params.forEach(
    (item) =>
      (obj[item] = function () {
        return this.$store.state[item];
      })
  );
  return obj;
};

const mapGetters = (params) => {
  if (!Array.isArray(params))
    throw new Error("Sorry, this version supports the array parameters");
  let obj = {};
  params.forEach(
    (item) =>
      (obj[item] = function () {
        return this.$store.getters[item];
      })
  );
  return obj;
};

const mapMutations = (params) => {
  if (!Array.isArray(params))
    throw new Error("Sorry, this version supports the array parameters");
  let obj = {};
  params.forEach(
    (item) =>
      (obj[item] = function (params) {
        return this.$store.commit(item, params);
      })
  );
  return obj;
};

const mapActions = (params) => {
  if (!Array.isArray(params))
    throw new Error("Sorry, this version supports the array parameters");
  let obj = {};
  params.forEach(
    (item) =>
      (obj[item] = function (params) {
        return this.$store.dispatch(item, params);
      })
  );
  return obj;
};

export { mapState, mapGetters, mapMutations, mapActions };
export default { Store, install };
```

###### 全局变量 & Vuex 数据

全局变量：依赖于全局变量的代码可维护性差，易被修改，难以追踪调试，容易导致命名空间污染

Vuex 数据：

- 响应式：Vuex 抽离组件的共享状态，统一管理，其状态存储为响应式，用于不同视图依赖于同一状态的复杂场景，确保状态的一致性和数据的可靠性
- 可预测性：Vuex 保证单向数据流，以可预测的方式保证状态变化，即通过 `dispatch` 触发  `actions` 来执行异步操作再通过 `commit` 提交 `mutations` 同步修改 `state`
- 模块化：基于 Flux 架构，使用单一状态树，所有组件状态均存储于一个全局对象 `store` 中，将状态树分割为 module，每个模块均有独立的 `state`、`getters`、`mutations` 和 `actions`，它们被注册到全局命名空间中，模块的状态被递归合并到全局对象 `store` 的状态树中，但其操作仍是局部的
- 工具集成：支持时间旅行调试，我们在任何时候可以回溯和重置状态变化，同时支持插件，允许执行自定义逻辑

###### 状态存储

1. 内存中的存储：Vuex 使用内存中的 JavaScript 对象存储状态，读写状态快速，但其无法持久化存储状态，在页面刷新或关闭时重置，但通过将状态变化同步到 localStorage 或 sessionStorage 中实现持久化存储状态
2. 存储共享数据：将需在不同组件或路由间共享的数据存储在 Vuex 的 `state` 中，状态持久存储且为响应式，在组件或路由中通过 `this.$store.state` 访问
3. 修改 Vuex 的 `state`：使用 Vue Router 导航守卫 `beforeEach` 等通过 `dispatch` 触发 `actions` 来执行异步操作再通过 `commit` 提交 `mutations` 同步修改 `state`

###### 判断是否 `commit`

1. Vuex 通过插件访问 `store` 内部，创建一个插件，它接收 `store` 为参数，通过 `store.subscribe` 方法监听 `mutations` 的提交

```js
const myPlugin = store => {
  store.subscribe((mutation, state) => {
    console.log(`Mutation type: ${mutation.type}`);
    console.log(`Mutation payload: ${mutation.payload}`);
  });
};

const store = new Vuex.Store({
  plugins: [myPlugin]
});
```

2. Vue.js devtools 支持时间旅行调试，追踪 Vuex 状态变化和 `mutations` 的提交

###### 使用场景

1. 组件或路由间共享状态
2. 组件通信
3. 全局单例状态：用户配置信息、应用全局配置
4. 持久化存储状态：Vuex 与 localStorage 或 sessionStorage 结合使用
5. 复杂业务逻辑：多视图共享和修改同一表单数据

###### Vuex 缺点

1. 样板代码：通过 `dispatch` 触发 `actions` 来执行异步操作再通过 `commit` 提交 `mutations` 同步修改 `state`，`mutations` 和 `actions` 显式分离，增加业务逻辑的复杂度，此外 Vuex 的严格规范也在一定程度上限制开发的灵活性
2. TS 兼容性：在设计之初未完全考虑 TS，尽管在 Vuex 后续版本改进了对 TS 的支持
3. 性能：Vuex 的状态更新基于响应式原理，但在频繁更新的业务场景下不是最优解
