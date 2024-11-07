缓存组件，切换时保持其状态而非销毁，避免不必要的渲染

```JavaScript
<keep-alive>
  <component :is="currentComponent"></component>
</keep-alive>
```

`include` 名称匹配成功的组件被缓存，`exclude` 反之，`max` 限制缓存组件的最大数量，超过部分按 LRU 算法调整

`created`：初始化 `cache` 和 `keys` 分别缓存组件虚拟 DOM 和 key
`mounted`：实时监听 `include` 和 `exclude` 黑白名单，若发生则调用 `pruneCache` 方法
`destroyed`：删除所有缓存

`src/core/components/keep-alive.js`

```js
export default {
  name: "keep-alive",
  abstract: true, // 判断组件是否渲染为真实 DOM
  props: {
    include: patternTypes,
    exclude: patternTypes,
    max: [String, Number],
  },
  created() {
    this.cache = Object.create(null); // 缓存虚拟 DOM
    this.keys = [];
  },
  mounted() {
    this.$watch("include", (val) =>
      pruneCache(this, (name) => matches(val, name))
    );
    this.$watch("exclude", (val) =>
      pruneCache(this, (name) => !matches(val, name))
    );
  },
  destroyed() {
    for (const key in this.cache) pruneCacheEntry(this.cache, key, this.keys);
  },
  render() {
    const slot = this.$slots.default;
    const vnode: VNode = getFirstComponentChild(slot);
    const componentOptions: ?VNodeComponentOptions =
      vnode && vnode.componentOptions;
    if (componentOptions) {
      const name: ?string = getComponentName(componentOptions);
      const { include, exclude } = this;
      if (
        (include && (!name || !matches(include, name))) ||
        (exclude && name && matches(exclude, name))
      )
        return vnode;
      const { cache, keys } = this;
      const key: ?string =
        vnode.key == null
          ? componentOptions.Ctor.cid +
            (componentOptions.tag ? `::${componentOptions.tag}` : "")
          : vnode.key;
      if (cache[key]) {
        vnode.componentInstance = cache[key].componentInstance;
        remove(keys, key);
        keys.push(key);
      } else {
        cache[key] = vnode;
        keys.push(key);
        if (this.max && keys.length > parseInt(this.max))
          pruneCacheEntry(cache, keys[0], keys, this._vnode);
      }
      vnode.data.keepAlive = true;
    }
    return vnode || (slot && slot[0]);
  },
};
```

`keep-alive` 自身组件通过判断组件实例上的 `abstract` 属性值，若为 `true` 直接跳过该实例，不在页面上渲染

`render` 获取 `keep-alive` 包裹的第一个子组件及其组件名，根据黑白名单是否匹配返回 `vnode`，根据组件 ID 和 Tag 生成的缓存 `key` 在缓存集合中查找是否已缓存过当前组件，若有则直接返回并更新缓存 `key` 在 `keys` 中的位置，否则往 `cache` 和 `keys` 中保存此组件及其缓存 `key`，根据 LRU 算法删除最近最少使用的实例，设置此组件实例的 `keep-alive` 属性为 `true`

```js
function pruneCacheEntry(
  cache: VNodeCache,
  key: string,
  keys: Array<string>,
  current?: VNode
) {
  const cached = cache[key];
  if (cached && (!current || cached.tag !== current.tag))
    cached.componentInstance.$destroy();
  cache[key] = null;
  remove(keys, key);
}
```

`pruneCacheEntry` 遍历集合，执行所有缓存组件的 `$destroy` 钩子，将 `cache` 对应 `key` 的内容设置为 `null`，删除 `keys` 中相应元素

![[Pasted image 20241107110525.png]]
