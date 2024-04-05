```JavaScript
const _mynew = function(fn, ...args) {
	// 创建一个新的空对象 `obj`
	// 将 `obj` 的原型设置为构造函数 `fn` 的原型对象
    const obj = Object.create(fn.prototype);
	// `apply` 确保 `this` 在构造函数内部指向新创建的对象 `obj`
    const res = fn.apply(obj, args);
    return typeof res === 'object' && res !== null ? res : obj;
};
```

为实现上述操作，`_mynew` 函数需知道两件事：

1. 哪个构造函数应该被调用 -> `fn`
2. 构造函数的参数是什么 -> `...args`
