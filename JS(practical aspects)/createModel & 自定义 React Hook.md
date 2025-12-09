将自定义 Hook 状态通过 Context 机制于组件树中共享：

```jsx
import { createContext, useContext } from "react";

const useStore = () => {
  const [count, setCount] = useState(0);
  return { count, setCount };
};

const createModel = (hook) => {
  const Context = createContext();
  const Provider = ({ children }) => {
    const value = hook();
    return <Context.Provider value={value}>{children}</Context.Provider>;
  };
  const useCustomContext = useContext(Context);
  return { Provider, useContext: useCustomContext };
};

const Store = createModel(useStore);

export const Demo = () => {
  const { count } = Store.useContext();
  return (
    <Store.Provider>
      <div>{count}</div>
      <AddCountButton />
    </Store.Provider>
  );
};

export const AddCountButton = () => {
  const { setCount } = Store.useContext();
  const onClick = () => setCount();
  return (
    <div>
      <button onClick={onClick}>+1</button>
    </div>
  );
};
```

###### 自定义 React Hook

1. 手写实现一次 setState 一次更新：

```js
import { useState } from "react";
import { flushSync } from "react-dom";

export const useImmediateState = (initialState) => {
  const [state, setState] = useState(initialState);
  const setImmediateState = (newState) => flushSync(() => setState(newState));
  return [state, setImmediateState];
};

export const DemoComponent = () => {
  const [count, setCount] = useImmediateState(0);
  const handleClick = () => {
    setCount((pre) => pre + 1);
    setCount((pre) => pre + 1);
  };
  return <button onClick={handleClick}>{count}</button>;
};
```

2. 手写类组件中 setState 的功能并支持设置回调函数：

```js
export const useStateWithCallback = (initialValue) => {
  const [state, setState] = useState(initialValue);
  const callbackRef = useRef(null);
  const setStateWithCallback = (newState, callback) => {
    setState(newState);
    callbackRef.current = callback;
  };
  useEffect(() => {
    if (callbackRef.current) {
      callbackRef.current(state);
      callbackRef.current = null;
    }
  }, [state]);
  return [state, setStateWithCallback];
};
```

3. 手写 `usePrevious` 以保存上一状态的值：以实现函数引用稳定和函数内部逻辑最新

```js
export const usePrevious = (value) => {
  const ref = useRef();
  useEffect(() => (ref.current = value), [value]);
  return ref.current;
};
```

4. 手写 `useStableCallback` 以实现函数引用稳定和函数内部逻辑最新：

```js
export const useStableCallback = (callback) => {
  const callbackRef = useRef(callback);
  useEffect(() => (callbackRef.current = callback), [callback]);
  const stableCallback = useCallback(
    (...args) => callbackRef.current(...args),
    []
  );
  return stableCallback;
};
```

5. 手写 `useFetch`：

* 竞态为发送的请求和返回的响应顺序不一一对应（后发先返）导致旧请求结果覆盖新请求结果
* `AbortController` 在发送新请求时中断旧请求，从网络层面过滤无效响应
* `requestId` 版本号确保最新请求的响应更新状态，从逻辑层面过滤无效响应

```js
export const useFetch = (url) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const requestId = useRef(0);
  useEffect(() => {
    const controller = new AbortController();
    const curId = ++requestId.current;
    const fetchData = async () => {
      setLoading(true);
      setError(null);
      try {
        const response = await fetch(url, { signal: controller.signal });
        if (!response.ok) throw new Error(response.status);
        const result = await response.json();
        if (curId === requestId.current) setData(result);
      } catch (error) {
        if (curId === requestId.current && error.name !== "AbortError")
          setError(error);
      } finally {
        if (curId === requestId.current) setLoading(false);
      }
    };
    fetchData();
    return () => controller.abort();
  }, [url]);
  return { data, loading, error };
};
```

5. 手写登陆拦截：

```js
export const useAuthRedirect = (redirectToLogin = true) => {
  const history = useHistory();
  useEffect(() => {
    const isLoggedIn = localStorage.getItem("auth_token");
    if (!isLoggedIn && redirectToLogin) history.push("/login");
  }, [history, redirectToLogin]);
};
```

6. 手写轮询：

```js
export const usePolling = (callback, interval, isActive = true) => {
  const [isPolling, setIsPolling] = useState(isActive);
  useEffect(() => {
    if (!isPolling) return;
    const intervalId = setInterval(() => callback(), interval);
    return () => clearInterval(intervalId);
  }, [callback, interval, isPolling]);
  const togglePolling = () => setIsPolling((pre) => !pre);
  return { isPolling, togglePolling };
};
```

7. 手写事件监听：

```js
export const useEventListener = (event, handler, element = window) => {
  useEffect(() => {
    if (typeof handler !== "function") return;
    element.addEventListener(event, handler);
    return () => element.removeEventListener(event, handler);
  }, [event, handler, element]);
};
```

8. 手写监听浏览器窗口

```js
export const useWindowSize = () => {
  const [windowSize, setWindowSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });
  useEffect(() => {
    const handleSize = () =>
      setWindowSize({ width: window.innerWidth, height: window.innerHeight });
    window.addEventListener("resize", handleSize);
    return () => window.removeEventListener("resize", handleSize);
  }, []);
  return windowSize;
};
```

9. 手写 `useHover`：

```js
export const useHover = () => {
  const [hovered, setHovered] = useState(false);
  const ref = useRef(null);

  useEffect(() => {
    const node = ref.current;
    if (!node) return;
    const handleMouseEnter = () => setHovered(true);
    const handleMouseLeave = () => setHovered(false);
    node.addEventListener("mouseenter", handleMouseEnter);
    node.addEventListener("mouseleave", handleMouseEnter);
    return () => {
      node.removeEventListener("mouseenter", handleMouseEnter);
      node.removeEventListener("mouseleave", handleMouseEnter);
    };
  }, []);
  return [ref, hovered];
};
```

10. 手写按钮锁即点击后 5s 内无法重复点击

```js
export const useBottonLock = (wait = 5000) => {
  const [isLocked, setIsLocked] = useState(false);
  const [shouldTimer, setShouldTimer] = useState(false);
  useEffect(() => {
    if (!shouldTimer) return;
    const timer = setTimeout(() => {
      setIsLocked(false);
      setShouldTimer(false);
    }, wait);
    return () => clearTimeout(timer);
  }, [shouldTimer, wait]);
  const handleClick = (fn) => {
    if (isLocked) return;
    fn && fn();
    setIsLocked(true);
    setShouldTimer(true);
  };
  return [isLocked, handleClick];
};
```

11. 手写 `useCounter` 定时器：

```js
export const useCounter = (initialVal = 0, step = 1, interval = 1000) => {
  const [count, setCount] = useState(initialVal);
  const timeRef = useRef(null);

  const start = () => {
    if (!timeRef.current)
      timeRef.current = setInterval(
        () => setCount((pre) => pre + step),
        interval
      );
  };

  const stop = () => {
    if (timeRef.current) {
      clearInterval(timeRef.current);
      timeRef.current = null;
    }
  };

  const reset = () => setCount(initialVal);

  useEffect(() => {
    return () => {
      if (timeRef.current) clearInterval(timeRef.current);
    };
  }, []);

  return { count, start, stop, reset };
};
```

12. 手写 `useRedoUndo`：

```js
export const useRedoUndo = (initialState) => {
  const [history, setHistory] = useState(initialState);
  const [curIndex, setCurIndex] = useState(0);
  const state = history[curIndex];
  const setState = (newState) => {
    const nextState =
      typeof newState === "function" ? newState(state) : newState;
    if (newState === state) return;
    const newHistory = history.slice(0, curIndex + 1);
    newHistory.push(nextState);
    setHistory(newHistory);
    setCurIndex(newHistory.length - 1);
  };
  const canUndo = curIndex > 0;
  const canRedo = curIndex < history.length - 1;
  const undo = () => {
    if (canUndo) setCurIndex(curIndex - 1);
  };
  const redo = () => {
    if (canRedo) setCurIndex(curIndex + 1);
  };
  return { state, setState, canUndo, canRedo, undo, redo };
};
```

执行撤销 -> 回到上一状态
执行重做 -> 未执行新操作，回到撤销前的状态，其仍保存于 `history` 数组中（在未通过 `setState` 更新状态的情况下）
