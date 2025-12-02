Redux：

* 全局 Store：Redux 通过 `createStore(reducer)` 创建全局 Store，通过 `reducer` 初始化 State
* 状态注入：Provider 根组件注入 Store 于 Context value，`useSelector` 订阅 Store 变化并通过浅比较判断选中的状态切片是否更新，更新时触发组件重新渲染
* 状态管理：通过 `dispatch` 触发 `action`，再将其传递给 `reducer`，`reducer` 根据当前 State 和 `action` 计算新 State，若存在多个 `reducer`，`combineReducers` 拆分状态树为切片 - reducer 映射，初始化时遍历子 `reducer` 生成各切片初始状态，dispatch 时将对应切片状态传递给子 `reducer`，再合并为完整状态树
* 中间件机制：位于 `dispatch` 和 `reducer` 间，基于函数柯里化与 `compose` 组合逻辑重写 `dispatch` 方法以拦截 `action`，如 `redux-thunk` 执行 `action` 函数并传递给 `dispatch/getState`，否则传递给下一中间件；`redux-saga` 基于生成器函数捕获副作用，通过 `take/put` 拦截并处理 `action` 

|      | Redux                                                                                                                                                                                                              | Zustand                                    |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------ |
| 状态容器 | `createStore(reducer)` 创建全局 Store，Provider 根组件注入 Store 于 Context value                                                                                                                                             | 纯 JavaScript 闭包创建 Store，无前端框架依赖            |
| 状态管理 | 通过 `dispatch` 触发 `action`，再将其传递给 `reducer`，`reducer` 根据当前 State 和 `action` 计算新 State，若存在多个 `reducer`，`combineReducers` 拆分状态树为切片 - reducer 映射，初始化时遍历子 `reducer` 生成各切片初始状态，dispatch 时将对应切片状态传递给子 `reducer`，再合并为完整状态树 | 通过 `action/reducer/setState` 更新状态          |
| 订阅机制 | `useSelector` 订阅 Store 变化并通过浅比较判断选中的状态切片是否变化，在切片变化时触发组件重新渲染                                                                                                                                                        | 监听状态切片变化，浅比较 `selector` 返回值，在切片变化时触发组件重新渲染 |
| 异步处理 | 通过 thunk/saga 中间件拦截 action 实现异步逻辑                                                                                                                                                                                  | 自定义 action 封装异步逻辑                          |
| 性能开销 | Context 层级 + 监听器全局批量更新 + 依赖中间件                                                                                                                                                                                     | selector 监听切片变化，浅比较避免组件不必要的渲染              |