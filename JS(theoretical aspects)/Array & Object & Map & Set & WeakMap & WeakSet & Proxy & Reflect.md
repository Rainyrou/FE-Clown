Array：有序集合，JavaScript 引擎在 Array 底层结合动态数组和 Object 的特性，Array 可根据所需自动增长，数组元素按顺序存储且可通过索引访问，Array 允许稀疏存储即数组元素可为空如 `[1, , 3]`

Object：键值对的集合，其键唯一，其值可为任何数据类型，JavaScript 引擎在 Object 底层使用哈希表来存储数据，每个 Object 的属性均有一个属性描述符（可通过 `Object.getOwnPropertyDescriptor` 获取），包含该属性的配置如可写性、可枚举性和可配置性，此外 Object 支持原型继承

Map：键值对的集合，类似 Object，但其键可为任何数据类型，JavaScript 引擎在 Map 底层使用哈希表来存储数据，执行增删查找操作的时间复杂度为 O(1)，若 Map 添加已存在的键，新值覆盖旧值，Map 依照元素的插入顺序进行遍历

Set：存储任何数据类型唯一值的集合，JavaScript 引擎在 Set 底层使用哈希表来存储数据，确保存储值唯一，执行增删查找操作的时间复杂度为 O(1)，若 Set 添加已存在的值，直接忽略

WeakMap：键值对的集合，其键为弱引用对象即引用数据类型，当无对 WeakMap 键的强引用时，这些键对应的对象可被垃圾回收，因为其键为弱引用对象，WeakMap 不支持枚举操作如 `forEach` 和迭代器等，因为键随时可能被垃圾回收，从而导致无法遍历所有键。JavaScript 引擎在 WeakMap 底层使用弱引用表来存储数据，弱引用表可跟踪对象的生命周期并在对象被垃圾回收时自动清理相应的键值对

WeakSet：存储任何数据类型唯一值的集合，其对象为弱引用对象即引用数据类型，当无对 WeakSet 对象的强引用时，这些键对应的对象可被垃圾回收，因为其键为弱引用对象，WeakSet 不支持枚举操作如 `forEach` 和迭代器等，因为键随时可能被垃圾回收，从而导致无法遍历所有键。JavaScript 引擎在 WeakSet 底层使用弱引用表来存储数据，弱引用表可跟踪对象的生命周期并在对象被垃圾回收时自动清理相应的键值对

Proxy：拦截并自定义 JavaScript 对象行为，其底层基于 JavaScript 引擎代理映射表，当构造 Proxy 实例时，JavaScript 引擎于代理表中注册包含目标对象引用、处理程序引用及代理类型标识的条目并将 JavaScript 引擎的原生内部操作与处理程序中的陷阱方法绑定，外部通过 Proxy 对象执行任何操作时，JavaScript 引擎判断该对象是否为 Proxy 实例，若是则通过代理映射表查找对应的处理程序，执行处理程序中相应的陷阱方法，这种访问路径拦截 + 代理映射机制使 Proxy 无需修改目标对象即可控制其行为

Reflect：执行 JavaScript 对象默认行为，其提供与 Proxy 陷阱方法对应的静态方法，JavaScript 引擎维护「Reflect 方法 - 原生内部操作」的映射表，Reflect 静态方法通过该映射表调用 JavaScript 引擎对应的底层内部操作

- API：Reflect 提供统一标准的 API，Proxy 拦截并自定义 JavaScript 对象行为，无需手动模拟其默认行为，直接调用对应 Reflect 静态方法即可复现 JavaScript 引擎原生逻辑，避免手动模拟兼容性差的问题
- 上下文：Reflect 的静态方法接收 `receiver` 参数，当目标对象存在 `getter/setter` 时，其内部的 `this` 绑定为 Proxy 对象即 `receiver` 而非目标对象，解决  Proxy 拦截时的 this 绑定丢失问题

```js
const proxy = new Proxy(target, handler);
```

- 获取属性 `get`
- 设置属性 `set`
- 删除属性 `deleteProperty`
- 检查属性 `has`
- 枚举属性 `enumerate`
- 调用函数 `apply`
- 调用构造函数创建实例 `construct`
- 定义&修改属性描述符 `Object.defineProperty`
- 获取属性描述符 `Object.getOwnPropertyDescriptor`
- 获取原型 `Object.getPrototypeOf`
- 设置原型 `Object.setPrototypeOf`
- 检查是否可扩展 `isExtensible`
- 阻止扩展 `preventExtensions`
