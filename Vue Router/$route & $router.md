`$router`：全局路由实例对象，包含所有路由的属性和方法，通过 `$router` 访问 Vue Router 的实例方法，从而控制应用的路由跳转如 `$router.push('/path')` -> 导航到不同的 URL，`$router.replace('/path')` -> 替换当前的 URL，`$router.go(n)` -> 在历史记录中前进或后退 n 步

`$route`：当前路由实例对象，包含当前激活的路由之属性和方法，每当路由变化时，`$route` 对象相应更新，它是只读的，不能通过修改 `$route` 来改变路由，但可通过 `$router` 实现

- `path`：当前路由的路径
- `params`：路由参数
- `query`：路由查询参数
- `hash`：hash
- `fullPath`：解析后的 URL，包含路由查询参数和 hash
- `matched`：嵌套路径片段的路由记录

