```JavaScript
const _myobjectFreeze = object => {
    if(object == null || (typeof object !== 'function' && typeof object !== 'object')) {
        return object;
    }
    // 获取对象的所有自有属性（不包括继承的属性）
	    const properties = Object.getOwnPropertyNames(object);
    for(let property of properties) {
        if(typeof object[property] === 'object' && object[property] != null) {
            _myobjectFreeze(object[property]);
        }
        let descriptor = Object.getOwnPropertyDescriptor(object, property);
        if(descriptor.writable) {
            descriptor.writable = false;
        }
        if(descriptor.configurable) {
            descriptor.configurable = false;
        }
        Object.defineProperty(object, property, descriptor);
    }
    Object.preventExtensions(object);
    return object;
};
```

冻结意味着对象属性的值不可更改，新的属性无法添加，且已有的属性不可删除或重新配置



