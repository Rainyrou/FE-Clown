`Object.freeze`：对象属性的值不可更改，无法添加新的属性，且已有的属性不可删除或重新配置

```JavaScript
const _myobjectFreeze = object => {
    if(object == null || (typeof object !== 'function' && typeof object !== 'object')) {
        return object;
    }
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



