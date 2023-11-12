```JavaScript
function _myobjectAssign(target, ...sources) {
    if(target == null) {
        throw new TypeError(`cannot convert undefined or null to object`);
    }
    const to = Object(target);
    // 第一次 `forEach` 遍历所有提供的源对象
    sources.forEach((source) => {
        if(source !== null) {
            Object.keys(source).forEach(key => {
                to[key] = source[key];
            })
        }
    })
    return to;
};
```

将一个或多个源对象的自身可枚举属性复制到目标对象上，并返回这个目标对象
