```js
const _deepClone = (target) => {
  if (typeof target !== "object" || target === null) return target;
  const map = new WeakMap();
  const stk = [];
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
  stk.push({ source: target, clone: rootClone });
  while (stk.length) {
    const { source, clone } = stk.pop();
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
              clone[key] = Array.isArray(value) ? [] : {};
              map.set(value, clone[key]);
              stk.push({ source: value, clone: clone[key] });
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

未处理循环引用导致无限递归或爆栈，递归写法在嵌套 10000 层时出现爆栈

```js
const _deepClone = (target, map = new WeakMap()) => {
  if (typeof target !== "object" || target === null) return target;
  const constructor = target.constructor;
  if (/^(Function|RegExp|Date|Map|Set)/i.test(constructor.name))
    return new constructor(target);
  const cloned = Array.isArray(target) ? [] : {};
  if (map.has(target)) return map.get(target);
  map.set(target, cloned);
  for (const key in target) {
    if (target.hasOwnProperty(key)) cloned[key] = _deepClone(target[key], map);
  }
  return cloned;
};
```

`i`：忽略大小写
`test`：测试字符串是否与正则表达式匹配


