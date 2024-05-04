通过 `new VueRouter` 创建的全局路由实例对象，包含所有路由的属性和方法，通过 `this.$router` 访问 Vue Router 的实例方法以控制路由跳转，底层上通过 `Vue.util.defineReactive` 使路由状态为响应式，Hash 模式下监听 `hashchange` 事件，在 History 模式下监听 `popstate` 事件，Vue Router 通过 `Vue.mixin` 方法将 `$router` 和 `$route` 注入到所有 Vue 组件实例中，在任何组件中通过 `this.$route` 来访问当前路由对象，自动捕获 URL 变化，当 URL 变化，Vue Router 根据路径匹配算法和当前 URL 解析定义的路由规则到 `route` 对象的映射，`<router-view>` 组件根据新路由动态渲染对应组件

###### Hash & History

1. Hash 模式：默认模式，使用 URL 的 hash 即 `#` 后的部分来模拟完整 URL，当 URL 改变时页面不会重新加载，基于浏览器的 `hashchange` API，使用 `window.addEventListener('hashchange', callback)` 监听 URL 的 hash 变化，当 hash 变化时，`hashchange` 事件触发，Vue Router 在回调函数中处理路由跳转，由于只是 hash 变化，无需 HTTP 请求，因此无需任何服务端配置
2. History 模式：传统 URL，基于浏览器的 `pushState`、`replaceState` 和 `popstate` API，使用 `window.addEventListener('popstate', callback)` 监听 URL 变化。当点击浏览器前进或后退按钮或 `history.back`、`history.forward` 和 `history.go(n)` 时，`popstate` 事件触发，此外在 `pushState` 或 `replaceState` 调用时 `popstate` 事件不会触发，因此 Vue Router 需在这些方法被调用时手动更新路由。旧版浏览器不支持 HTML5 History API，除根目录外的 URL 在服务端上不存在相应资源，服务端需配置 URL 重写规则以支持 SPA 的这种行为，确保所有路由均指向同一入口文件如 `index.html`

`nginx.conf`

```js
location / {
  try_files $uri $uri/ /index.html;
}
```

Node.js

```js
const express = require('express');
const app = express();
const path = require('path');
app.use(express.static(__dirname + '/public'));
app.get('*', (req, res) => res.sendFile(path.resolve(__dirname, 'public', 'index.html')));
app.listen(3000);
```

###### 路由传值

1. 查询参数：以 `?key=value` 的形式

```JavaScript
this.$router.push({ path: '/target', query: { name: 'Clown' } });
this.$route.query.name;
```

2. 路由参数：以 `/:paramName` 的形式

```JavaScript
{ path: '/user/:id', component: UserComponent };
this.$router.push({ path: `/user/${userId}` });
this.$route.params.id;
```

3. 路由元数据：为路由指定额外元数据

```JavaScript
{ path: '/admin', component: AdminComponent, meta: { requiresAuth: true } }
this.$route.meta.requiresAuth;
```

4. Vuex & Pinia
5. localStorage & sessionStorage

###### `$router` & `$route`

`$router`：通过 `new VueRouter` 创建的全局路由实例对象，包含所有路由的属性和方法，通过 `this.$router` 访问 Vue Router 的实例方法以控制路由跳转如 `$router.push('/path')` -> 导航到不同 URL，`$router.replace('/path')` -> 替换当前 URL，`$router.go(n)` -> 在历史记录中前进或后退 n 步

`$route`：当前路由实例对象，包含当前路由的属性和方法，只读，无法通过修改 `this.$route` 来改变路由，每当路由变化时，`route` 对象更新，反映当前路由状态

- `path`：当前路由路径
- `params`：路由参数
- `query`：路由查询参数
- `hash`：hash
- `fullPath`：解析后的 URL，包含路由查询参数和 hash
- `matched`：嵌套路径片段的路由记录

###### 导航守卫

1. 全局守卫:

   - `router.beforeEach`: 在导航触发前被全局调用
   - `router.afterEach`: 在导航触发后被全局调用
   - `router.beforeResolve`: 在解析导航前被全局调用

2. 路由独享守卫：`beforeEnter`
3. 组件内守卫:

   - `beforeRouteEnter`: 唯一不能获取到组件实例的守卫，组件渲染前调用
   - `beforeRouteUpdate`: 当前路由改变，但该组件复用时调用
   - `beforeRouteLeave`: 导航离开该组件对应路由时调用

###### 生命周期

1. 当点击 `<router-link>` 或通过 `router.push` 或 `router.replace` 触发路由，开始导航
2. 调用 `beforeRouteLeave`
3. 调用 `beforeEach`
4. 调用 `beforeRouteUpdate`
5. 调用 `beforeEnter`
6. 解析异步路由组件
7. 调用 `beforeRouteEnter`
8. 调用 `beforeResolve`
9. 若没有调用 `next(false)`，则确认导航
10. 调用 `afterEach`
11. DOM 更新
12. 调用 `beforeRouteEnter` 中传入 `next` 回调，创建好的组件实例作为回调函数参数

![[Pasted image 20240504181930.png]]

###### 动态定义路由

`router.addRoute`：动态添加路由
`router.removeRoute(routeName)`：删除路由

###### 动态组件 & 懒加载

```js
const router = new VueRouter({
  routes: [
    {
      path: '/dashboard',
      component: () => import('./components/Dashboard.vue')
    },
    {
      path: '/user/:userId',
      component: () => import('./components/UserProfile.vue')
    }
  ]
});
```

###### 局限性

1. 动态路由：使用动态路由时，当参数变化但页面组件不变时，默认情况下组件不会重新渲染
2. SEO：Vue Router 默认支持 SPA，动态加载页面内容，SEO 受到影响，爬虫可能无法正确执行 JavaScript 或解析页面内容
3. 路由匹配：Vue Router 定义多个匹配同一路径的路由规则时可能不会按照预期匹配正确的路由
4. 嵌套路由：配置嵌套路由可维护性差
5. 路由历史管理：Vue Router 不支持完整的路由历史管理功能如清除历史记录或管理历史记录的长度等

###### 不使用 Vue Router

原生 HTML 或 JavaScript：使用 `window.location.href` 或 `window.location.replace` 手动进行页面跳转或手动修改 `window.location.hash`，使用 `window.onhashchange` 事件监听 URL hash 变化来触发视图更新
