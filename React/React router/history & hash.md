###### history

基于浏览器的 History API

1. 导航：利用 `history.push` 和 `history.replace` 方法改变当前的 URL，`push` 方法在历史堆栈中添加新条目，而 `replace` 方法替换当前条目
2. 监听 URL 变化：利用 `history.listen` 方法来监听 URL 变化，触发某些操作，如页面跳转或者数据加载
3. 获取当前位置：利用 `history.location` 对象获取当前的 URL 信息
4. 导航控制：利用 `history.goBack`、`history.goForward` 和 `history.go` 方法来控制浏览器历史的前进和后退

###### hash

- URL 包含 `#` 符号，`#` 后的部分即 hash 值不会被发送到服务器，可用于不支持 History API 的浏览器，或在无需服务端配置的情况下实现 SPA 的路由
- 利用 URL 的 hash `window.location.hash` 和 `hashchange` 事件来保持 UI 和 URL 同步

#### 优点

- 不需要服务器端配置，可以直接通过文件路径打开 HTML 文件，适用于静态文件服务器或者不方便进行服务器配置的场景
- 兼容旧版浏览器

#### 缺点

- URL 中包含 `#`，可能不够美观。
- 受限于 hash 值的特性，只能用于前端路由控制。

```TSX
import React from 'react';
import { useHistory } from 'react-router-dom';

function MyComponent() {
  let history = useHistory();

  function handleGoBack() {
    history.goBack();
  }

  function handleGoHome() {
    history.push('/home');
  }

  return (
    <div>
      <button onClick={handleGoBack}>Go Back</button>
      <button onClick={handleGoHome}>Go Home</button>
    </div>
  );
}

export default MyComponent;
```

- `<BrowserRouter>` 和 `<HashRouter>` 组件内部分别使用 HTML5 的 `history` 和哈希路由来控制路由
- 当使用 React Router 的 `history` 对象更改 URL 时，它不会引起页面的重新加载，而是触发相应的 React 组件重新渲染，从而更新页面内容
