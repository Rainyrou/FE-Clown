1. 手写轮询：

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

2. 手写登陆拦截：

```js
export const useAuthRedirect = (redirectToLogin = true) => {
  const history = useHistory();
  useEffect(() => {
    const isLoggedIn = localStorage.getItem("auth_token");
    if (!isLoggedIn && redirectToLogin) history.push("/login");
  }, [history, redirectToLogin]);
};
```

3. 手写事件监听：

```js
export const useEventListener = (event, handler, element = window) => {
  useEffect(() => {
    if (typeof handler !== "function") return;
    element.addEventListener(event, handler);
    return () => element.removeEventListener(event, handler);
  }, [event, handler, element]);
};
```

4. 手写监听浏览器窗口

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

5. 手写类组件中 setState 的功能并支持设置回调函数：

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

6. 手写 `useQuery`：

7. 手写 `usePrevious` 以保存上一状态的值

```js
export const usePrevious = (value) => {
  const ref = useRef();
  useEffect(() => (ref.current = value), [value]);
  return ref.current;
};
```

8. 手写按钮锁即点击后 5s 内无法重复点击

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

9. 手写 `useCounter` 定时器：

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

10. 手写 `useHover`：

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