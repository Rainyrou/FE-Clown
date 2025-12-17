通过 `new VueRouter` 创建全局路由实例对象，底层上通过 `defineReactive` 将路由状态设置为响应式，Hash 模式监听 `hashchange` 事件，History 模式监听 `popstate` 事件，通过 `mixin` 机制在组件的生命周期中将 `$router` 和 `$route` 注入至组件实例，在组件中通过 `this.$router/$route` 访问全局/当前路由对象，解析路径，根据路径匹配算法和路由规则将路径映射至当前路由对象，`router-view` 组件根据路由变化动态渲染对应组件

1. `$router`：全局路由实例对象，包含所有路由的属性和方法，通过 `this.$router` 访问
2. `$route`：当前路由实例对象，包含当前路由的属性和方法，通过 `this.$route` 访问
3. `router.addRoute` -> 添加路由 `router.removeRoute` -> 删除路由
4. Hash 模式：默认模式，通过 URL hash 即 `#` 后的部分模拟完整 URL，当 URL hash 变化时页面不刷新，通过事件监听器监听 `hashchange` 事件实时捕获 hash 变化，Vue Router 解析提取当前 hash 值，根据路径匹配算法和路由规则将路径映射至当前路由对象，在回调中执行路由跳转逻辑
5. History 模式：传统 URL，服务端解析完整路径，其基于浏览器 `popstate/pushState/replaceState`，通过事件监听器监听 `popstate` 事件，服务端配置 URL 规则以支持 SPA，确保所有路由指向同一入口文件

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

路由传值

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

3. 路由元数据：为路由添加额外元数据

```JavaScript
{ path: '/admin', component: AdminComponent, meta: { requiresAuth: true } }
this.$route.meta.requiresAuth;
```

4. Vuex & Pinia
5. localStorage & sessionStorage

导航守卫：

1. 全局守卫:

   - `router.beforeEach`: 在导航触发前被全局调用
   - `router.afterEach`: 在导航触发后被全局调用
   - `router.beforeResolve`: 在解析导航前被全局调用

2. 路由独享守卫：`beforeEnter`
3. 组件内守卫:

   - `beforeRouteEnter`: 唯一不能获取到组件实例的守卫，组件渲染前调用
   - `beforeRouteUpdate`: 当前路由改变，但该组件复用时调用
   - `beforeRouteLeave`: 导航离开该组件对应路由时调用

生命周期

1. 当点击 `<router-link>` 或通过 `router.push` 或 `router.replace` 触发路由，开始导航
2. 调用 `beforeRouteLeave`
3. 调用 `beforeEach`
4. 调用 `beforeRouteUpdate`
5. 调用 `beforeEnter`
6. 解析异步路由组件
7. 调用 `beforeRouteEnter`
8. 调用 `beforeResolve`
9. 若无调用 `next(false)`，则确认导航
10. 调用 `afterEach`
11. DOM 更新
12. 调用 `beforeRouteEnter` 传入的 `next` 回调，以创建的组件实例为参数

![[Pasted image 20240504181930.png]]


动态组件 & 懒加载

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


