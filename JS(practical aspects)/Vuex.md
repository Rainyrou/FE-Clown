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

构造函数：通过 `observable` 将 `state` 对象转换为响应式
`install`：将 `_Vue` 设置为全局 Vue 构造函数，通过 `mixin` 注入 `$store` 到所有组件实例中，在每个组件的 `beforeCreate` 钩子中，若组件选项存在 `store`，则将该 `store` 实例赋值给所有组件实例的 `$store` 属性，这样每个组件均可通过 `this.$store` 访问到 Vuex Store
