```JavaScript
function _objectCreate(proto, propertiesObject) {
  if (typeof proto !== "object" && typeof proto !== "function")
    throw new TypeError(`${proto} is not an object or null`);
  const fn = function () {};
  fn.prototype = proto;
  const obj = new fn();
  if (propertiesObject !== undefined)
    Object.defineProperties(obj, propertiesObject);
  return obj;
}
```

测试用例：

验证能创建具有正确原型的对象：

```JavaScript
const prototype = { foo: 'bar' };
const obj = _objectCreate(prototype);
console.assert(Object.getPrototypeOf(obj) === prototype, "Test 1 failed: Incorrect prototype.");
```

验证可在新对象上定义属性：

```JavaScript
const properties = {
    prop: {
        value: 42,
        writable: true
    }
};
const objWithProps = _objectCreate(prototype, properties);
console.assert(objWithProps.prop === 42, "Test 2 failed: Property value is incorrect.");
```
