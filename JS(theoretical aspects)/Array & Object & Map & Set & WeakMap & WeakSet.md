Array：有序集合，JavaScript 引擎在 Array 底层结合动态数组和 Object 的特性，Array 可根据所需自动增长，数组元素按顺序存储且可通过索引访问，Array 允许稀疏存储即数组元素可为空如 `[1, , 3]`
Object：键值对的集合，其键唯一，其值可为任何数据类型，JavaScript 引擎在 Object 底层使用哈希表来存储数据，每个 Object 的属性均有一个属性描述符（可通过 `Object.getOwnPropertyDescriptor` 获取），包含该属性的配置如可写性、可枚举性和可配置性，此外 Object 支持原型继承
Map：键值对的集合，类似 Object，但其键可为任何数据类型，JavaScript 引擎在 Map 底层使用哈希表来存储数据，执行增删查找操作的时间复杂度为 O(1)，若 Map 添加已存在的键，新值覆盖旧值，Map 依照元素的插入顺序进行遍历
Set：允许存储任何数据类型唯一值的集合，JavaScript 引擎在 Set 底层使用哈希表来存储数据，确保存储值均唯一，执行增删查找操作的时间复杂度为 O(1)，若 Set 添加已存在的值，直接忽略
WeakMap：键值对的集合，其键为弱引用对象即引用数据类型，当无对 WeakMap 键的强引用时，这些键对应的对象可被垃圾回收，因为其键为弱引用对象，WeakMap 不支持枚举操作如 `forEach` 和迭代器等，因为键随时可能被垃圾回收，从而导致无法遍历所有键。JavaScript 引擎在 WeakMap 底层使用弱引用表来存储数据，弱引用表可跟踪对象的生命周期并在对象被垃圾回收时自动清理相应的键值对
WeakSet：允许存储任何数据类型唯一值的集合，其对象为弱引用即引用数据类型，当无对 WeakSet 对象的强引用时，它们可被垃圾回收，因为其键为弱引用对象，WeakSet 不支持枚举操作如 `forEach` 和迭代器等，因为键随时可能被垃圾回收，从而导致无法遍历所有键。JavaScript 引擎在 WeakSet 底层使用弱引用表来存储数据，弱引用表可跟踪对象的生命周期并在对象被垃圾回收时自动清理相应的键值对
