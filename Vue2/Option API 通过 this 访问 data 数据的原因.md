Vue 2 的选项式 API 允许开发者在组件的 `data` 函数中定义数据，然后通过 `this` 关键字在组件的其他部分访问这些数据，这一特性是 Vue 实例化组件时内部处理逻辑的结果

当 Vue 组件实例化时，内部会进行一系列初始化步骤：在创建组件时，Vue 首先调用 `data` 函数获取定义的数据对象，再通过 `Object.defineProperty`  递归劫持 `data` 所有属性，为其添加 `getter` 和 `setter`。当你通过 `this` 访问一个属性时，实际上你访问的是 `data` 对象中相应的属性。这一步是通过在 Vue 实例上定义同名属性，并在这些属性的 getter/setter 中访问 `data` 对象中对应属性的 getter/setter 来实现的

