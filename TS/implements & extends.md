`implements`：用于在类中实现一至多个接口，`implements` 是一种约束，类只获取接口的结构，不继承任何具体实现。`implements` 和接口在 JavaScript 中无直接对应，在底层上 TypeScript 编译器将 TS 转换为 JS 代码时移除 `implements` 和接口
`extends`：子类继承父类的所有属性和方法并允许扩展父类的属性和方法，在底层上 TypeScript 编译器将 `extends` 转换成原型继承的 JS 代码，使得子类实例能访问父类的所有属性和方法
