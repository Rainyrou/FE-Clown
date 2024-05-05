`useMemo` 用于记忆计算结果值，可避免在每次渲染时重新计算，它接收一个回调函数和一个依赖项数组，返回一个计算结果，只有当依赖项发生变化时，该值才被重新计算

```JSX
const memoizedValue = useMemo(() => computedValue(a, b), [a, b]);
```