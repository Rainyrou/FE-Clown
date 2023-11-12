根据包名，在指定空间中创建对象

输入描述：namespace({a: {test: 1, b: 2}}, 'a.b.c.d')

输出描述：{a: {test: 1, b: {c: {d: {}}}}}

![[1693408089298.png]]

- 使用 `split` 方法将 `sPackage` 字符串分割为数组

复合赋值语句，可分为两部分：

- 使用 `Object.assign` 函数将 `tmpWrap[item]` 的属性复制到一个新的空对象中。如果`tmpWrap[item]` 不存在，`Object.assign` 返回一个空对象
- `tmpWrap[item] = ...`: 更新或创建 `tmpWrap` 对象的 `item` 属性，并将其设置为上一步得到的对象
- `tmpWrap = tmpWrap[item]`: 更新 `tmpWrap` 引用，以便它现在指向新的（或更新的）嵌套对象。如此一来，在下次循环时，可以继续在当前层级的基础上构建更深层的命名空间

###### 为什么不能直接 `tmpWrap = tmpWrap[item]` ，而要在最后加上 `Object.assign({}, tmpWrap[item]);` ?

[Object.assign() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

`Object.assign` 方法用于将一个或多个源对象的可枚举属性复制到目标对象，并返回目标对象

在此场景下，`Object.assign({}, tmpWrap[item])` 的目的是复制 `tmpWrap[item]` 对象的属性到一个新的空对象中，并返回该对象。这确保我们不会直接修改原始 `oNamespace` 对象中可能已存在的任何对象。我们的目标是维持现有属性，并只添加或修改所需的部分
