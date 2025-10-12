1. 手写类组件中 setState 的功能并支持设置回调函数：

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

2. 手写 `usePrevious` 以保存上一状态的值

```js
export const usePrevious = (value) => {
  const ref = useRef();
  useEffect(() => (ref.current = value), [value]);
  return ref.current;
};
```

3. 手写 `useFetch`：

```js
export const useFetch = (url) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  useEffect(() => {
    const controller = new AbortController();
    const fetchData = async () => {
      setLoading(true);
      setError(null);
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error(response.status);
        const result = await response.json();
        setData(result);
      } catch (error) {
        setError(error);
      } finally {
        setLoading(true);
      }
    };
    url && fetchData();
    return () => controller.abort();
  }, [url]);
  return { data, loading, error };
};
```

4. 手写登陆拦截：

```js
export const useAuthRedirect = (redirectToLogin = true) => {
  const history = useHistory();
  useEffect(() => {
    const isLoggedIn = localStorage.getItem("auth_token");
    if (!isLoggedIn && redirectToLogin) history.push("/login");
  }, [history, redirectToLogin]);
};
```

5. 手写轮询：

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

6. 手写事件监听：

```js
export const useEventListener = (event, handler, element = window) => {
  useEffect(() => {
    if (typeof handler !== "function") return;
    element.addEventListener(event, handler);
    return () => element.removeEventListener(event, handler);
  }, [event, handler, element]);
};
```

7. 手写监听浏览器窗口

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

8. 手写 `useHover`：

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

9. 手写按钮锁即点击后 5s 内无法重复点击

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

10. 手写 `useCounter` 定时器：

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

11. 手写 `useRedoUndo`：

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
