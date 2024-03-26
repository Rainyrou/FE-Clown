`useCallback` 基于闭包和 React 的 Hooks 机制，它接受一个回调函数和一个依赖项数组，返回一个记忆化的版本的回调函数，只有当依赖项发生变化时，该回调函数才会更新。`useCallback` 利用 React 的 Hooks 机制来存储它的参数---回调函数和依赖项数组。当组件重新渲染时，`useCallback` 检查依赖项数组，若依赖项与上一次渲染时完全相同，则返回上一次渲染时缓存的回调函数，否则 `useCallback` 更新缓存的回调函数并返回新的函数实例

```JavaScript
const memoizedCallback = useCallback(() => doSomething(a, b), [a, b]);
```

当依赖项数组中包含对象时，`useCallback` 等 Hook 的行为基于这些对象的引用比较，若对象的属性值变化，但对象的引用不变即未创建一个新的对象，`useCallback` 不会认为依赖项发生改变，因此不会返回一个新的回调函数

```jsx
const [obj, setObj] = useState({ count: 0 });
const callback = useCallback(() => console.log(obj.count), [obj]); 
```

即使 `obj.count` 的值改变，但若 `obj` 对象的引用不变，`useCallback` 不会返回一个新的回调函数

若要在对象的属性改变时获得一个新的回调函数，需确保对象的引用在属性改变时也发生改变，这可以通过在状态更新逻辑中创建一个新的对象来实现：

```jsx
setObj(prevObj => ({ ...prevObj, count: prevObj.count + 1 }));
```

