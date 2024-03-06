`useLayoutEffect` 的行为和 `useEffect` 非常相似，但它在所有 DOM 更新后同步调用，在页面渲染前执行，它适用于需要立即更新 DOM 或对 DOM 布局有直接影响的操作，可以避免可能的闪烁效果
