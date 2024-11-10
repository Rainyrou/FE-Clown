`ref` 用于将非响应式原始值转化为响应式引用，通过 `.value` 访问和修改，适用于基本和引用数据类型，在模板中使用时自动解引用
`reactive` 用于将非响应式原始值转化为响应式代理，适用于引用数据类型，在模板中使用时无需解引用

```ts
export function shallowRef(value?: unknown) {
  return createRef(value, true);
}

export function ref(value?: unknown) {
  return createRef(value, false);
}

function createRef(rawValue: unknown, shallow: boolean) {
  return isRef(rawValue) ? rawValue : new RefImpl(rawValue, shallow);
}

class RefImpl<T> {
  private _rawValue: T; // 存储原始值
  private _value: T; // 存储响应式值
  public dep?: Dep = undefined; // 用于依赖跟踪
  public readonly __v_isRef = true; // 是否是 ref 实例

  constructor(value: T, public readonly __v_isShallow: boolean) {
  	this._rawValue = __v_isShallow ? value : toRaw(value);
    this._value = __v_isShallow ? value : toReactive(value);
  }

  get value() {
    trackRefValue(this); // 依赖追踪，当 ref 值变化时，依赖于该 ref 的组件/副作用函数重新执行
    return this._value; // 返回存储的响应式值
  }

  set value(newVal) {
    const useDirectValue =
      this.__v_isShallow || isShallow(newVal) || isReadonly(newVal);
    newVal = useDirectValue ? newVal : toRaw(newVal);
    if (hasChanged(newVal, this._rawValue)) {
      this._rawValue = newVal;
      this._value = useDirectValue ? newVal : toReactive(newVal);
      triggerRefValue(this, DirtyLevels.Dirty, newVal); // 依赖更新
    }
  }
}
```
	
```ts
function reactive(target) {
  if (target && target.__v_isReactive) return target;
  return createReactiveObject(
    target,
    false,
    mutableHandlers,
    mutableCollectionHandlers,
    reactiveMap
  );
}

function createReactiveObject(
  target,
  isReadonly,
  baseHandlers,
  collectionHandlers,
  proxyMap
) {
  if (!isObject(target)) return target;
  const existingProxy = proxyMap.get(target);
  if (existingProxy) return existingProxy;
  const proxy = new Proxy(target, baseHandlers);
  proxyMap.set(target, proxy);
  return proxy;
}
```
