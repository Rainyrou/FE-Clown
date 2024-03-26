`react-router-dom` 用于构建在浏览器中运行的网页应用，提供一些特定组件如 `<Link>`、`<BrowserRouter>` 和 `<Route>`。其基于 React 的组件模型，监听浏览器地址栏的 URL 变化，并将 URL 与 `<Route>` 组件定义的路径模式相匹配。当 URL 匹配到某个路径时，相应的组件被渲染到页面上，这种机制使得在 SPA 中实现页面切换变得容易，且无需重新加载页面

1. 声明式路由: 允许使用 JSX 语法直接在组件中定义路由规则
2. 动态路由匹配: 支持使用参数化的 URL 路径来动态匹配路由
3. 导航控制: 提供编程式的导航控制，如前进、后退和跳转到指定路径
4. 嵌套路由: 支持在应用的不同层级定义路由规则
5. 位置感知: 组件能够根据当前的 URL 位置渲染不同的 UI

###### 常用组件

- `<BrowserRouter>`: 使用 HTML5 的 history API 提供的路由功能
- `<Route>`: 根据 URL 的路径来渲染相应的组件
- `<Link>`: 提供声明式、可访问的导航
- `<Switch>`: 用于渲染与当前 URL 匹配的第一个 `<Route>` 或 `<Redirect>`
- `<Redirect>`: 用于导航到不同的 URL

###### 示例代码

```TSX
import React from 'react';
import { BrowserRouter as Router, Route, Link, Switch } from 'react-router-dom';

function Home() {
  return <h2>Home Page</h2>;
}

function About() {
  return <h2>About Page</h2>;
}

function App() {
  return (
    <Router>
      <nav>
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/about">About</Link></li>
        </ul>
      </nav>

      <Switch>
        <Route path="/about">
          <About />
        </Route>
        <Route path="/">
          <Home />
        </Route>
      </Switch>
    </Router>
  );
}

export default App;
```
