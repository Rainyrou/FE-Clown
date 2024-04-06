`Proxy` 用于创建响应式数据，其为 Vue3 响应式原理的核心，取代 Vue2 `Object.defineProperty` 方法，其能拦截和监视对象的任意操作，支持更多的数据结构

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

###### 局限性

1. ES6 的特性，不支持 IE 浏览器
2. 不像 `Object.defineProperty`，`Proxy` 无法通过 polyfill 在不支持的环境中实现

###### 底层原理

 * Vue2  响应式的特点是依赖收集，数据可变，自动派发更新，初始化时通过 `Object.defineProperty`  递归劫持  `data` 所有属性，为其添加 `getter` 和 `setter`。每个组件实例关联一个 `watcher` 实例，在组件渲染时进行依赖收集，并在依赖项的 `setter` 被调用时通知 `watcher` 重新渲染
- Vue3 使用原生 `Proxy`  重构响应式，`Proxy`  修复 Vue2 响应式存在的缺陷，它可以拦截对象的任意操作（包括增删对象属性、数组索引赋值和修改数组长度等），支持更多数据结构，且不再一开始就递归劫持对象属性，而是代理第一层对象本身，运行时才递归，按需代理，用 `effect`  副作用函数来代替 Vue2 的 `watcher`，用 `trackMap`  代替 Vue2 的 `Dep` 统一管理依赖，无需再维护大量依赖关系，性能显著提升

