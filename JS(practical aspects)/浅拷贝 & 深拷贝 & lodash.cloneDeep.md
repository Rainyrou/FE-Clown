```js
const _deepClone = (target) => {
  if (typeof target !== "object" || target === null) return target;
  const map = new WeakMap();
  const stack = [];
  const rootClone = (() => {
    const constructor = target.constructor;
    if (/^(Function|RegExp|Date|Map|Set)$/i.test(constructor.name)) {
      const cloned = new constructor(target);
      map.set(target, cloned);
      return cloned;
    }
    const cloned = Array.isArray(target) ? [] : {};
    map.set(target, cloned);
    return cloned;
  })();
  stack.push({ source: target, clone: rootClone });
  while (stack.length > 0) {
    const { source, clone } = stack.pop();
    for (const key in source) {
      if (source.hasOwnProperty(key)) {
        const value = source[key];
        if (typeof value === "object" && value !== null) {
          const constructor = value.constructor;
          if (/^(Function|RegExp|Date|Map|Set)$/i.test(constructor.name)) {
            clone[key] = new constructor(value);
            map.set(value, clone[key]);
          } else {
            if (map.has(value)) {
              clone[key] = map.get(value);
            } else {
              const clonedChild = Array.isArray(value) ? [] : {};
              clone[key] = clonedChild;
              map.set(value, clone[key]);
              stack.push({ source: value, clone: clone[key] });
            }
          }
        } else {
          clone[key] = value;
        }
      }
    }
  }
  return rootClone;
};

function createDeepObject(depth) {
  let root = {},
    cur = root;
  for (let i = 0; i < depth; i++) {
    cur["nested"] = {};
    cur = cur.nested;
  }
  return root;
}

const obj = createDeepObject(10000);
console.time("Deep Clone");
const cloned = _deepClone(obj);
console.timeEnd("Deep Clone");
console.log(_deepClone(42));
console.log(_deepClone(new Date()));
console.log(_deepClone([1, 2, 3, [4, 5]]));
console.log(_deepClone({ a: 1, b: { c: 2 } }));
```

`lodash.cloneDeep` 相比手写深拷贝有以下显著优势：

1. 处理更多数据类型如 `TypedArray`、`Buffer` 和 `ArrayBuffer` 等，确保深拷贝的完整性，而这些在手写深拷贝实现中往往被忽略，`lodash.cloneDeep` 通过内部的类型检测机制对不同数据类型进行相应处理，确保拷贝后的数据与原数据保持一致
2. `lodash.cloneDeep` 进行多种性能优化，为复杂数据结构提供良好性能如通过 `WeakMap` 记录已拷贝的对象，避免循环引用导致的无限递归问题，尽管手写深拷贝也使用 `WeakMap`，但 `lodash.cloneDeep` 在底层实现上性能更优，通过缓存机制减少对重复对象的深拷贝操作，此外其通过分析对象的结构，减少对未变化部分的重复拷贝即按需拷贝
3. `lodash.cloneDeep` 经过大量测试和社区验证，具有极强的鲁棒性和可靠性，在更广泛的业务场景下保持稳定，手写深拷贝实现在一些边缘和极端情况下可能出现问题
4. `lodash` 本身作为最广泛使用的工具库之一，拥有极其庞大的用户和社区支持，其实现不断更新迭代且及时修复 bug 并引入新特性，而手写深拷贝缺乏此种持续迭代和社区反馈

```JavaScript
const _shallowClone = target => {
    if(target === null || typeof target !== 'object') return target;
    const constructor = target.constructor;
    if(/^(Function|RegExp|Date|Map|Set)$/i.test(constructor.name)) return target;
    const clonedTarget = Array.isArray(target) ? [] : {};
    for(let prop in target) {
        if(target.hasOwnProperty(prop)) clonedTarget[prop] = target[prop];
    }
    return clonedTarget;
};
```

1. 浅拷贝只拷贝指向引用数据类型对象的堆内存之指针，拷贝的是引用即对象的第一层属性，而深拷贝拷贝引用数据类型对象的所有属性至一个新的对象中，若其属性仍引用其他对象，则递归进行深拷贝即递归拷贝对象的所有层级
2. WeakMap  为一组键值对的集合，其键为引用数据类型，其持有的是对象的弱引用，当无对 WeakMap 键的强引用时，这些键对应的对象可被垃圾回收

未处理循环引用导致无限递归或爆栈

递归写法在嵌套 10000 层时出现爆栈

```js
const _deepClone = (target, map = new WeakMap()) => {
  if (typeof target !== "object" || target === null) return target;
  const constructor = target.constructor;
  if (/^(Function|RegExp|Date|Map|Set)$/i.test(constructor.name))
    return new constructor(target);
  const clonedTarget = Array.isArray(target) ? [] : {};
  if (map.has(target)) return map.get(target);
  map.set(target, clonedTarget);
  for (const key in target)
    if (target.hasOwnProperty(key))
      clonedTarget[key] = _deepClone(target[key], map);
  return clonedTarget;
};
```

`i`：忽略大小写
`test`：测试字符串是否与正则表达式匹配


