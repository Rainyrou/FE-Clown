`instanceof` 用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上

```JavaScript
// target 为实例对象，fn 为构造函数
const _myinstanceof = (target, fn) => {
    let proto = Object.getPrototypeOf(target);
    while(proto) {
        if(proto === fn.prototype) return true;
        proto = Object.getPrototypeOf(proto);
    }
    return false;
};
```