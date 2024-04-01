##### 路由的种类

1. Hash 模式

- 默认模式，使用 URL 的 hash（即 `#` 符号后面的部分）来模拟一个完整的 URL

```JavaScript
http://example.com/#/user/id
```

- 几乎所有浏览器都支持此模式
- 基于 URL 中的 hash，当 URL 改变时页面不会重新加载
- 由于只是 hash 变化，无需 HTTP 请求，因此无需任何服务端配置
* 底层原理：基于浏览器的 `hashchange` API

2. History 模式

* 没有 hash，看起来更像传统 URL

```JavaScript
http://example.com/user/id
```

* 旧浏览器不支持 HTML5 History API 
- 服务端需配置 URL 重写规则，确保所有路由都指向同一个 HTML 文件
- 底层原理：基于浏览器的 History API `pushState`、`replaceState` 和 `popstate`

##### 基本特性

1. 动态路由匹配：

可以通过动态路径参数来响应各种模式的 URL

```JavaScript
const router = new VueRouter({
	routes: [
		{ path: '/user/:id', component: User }
	]
})
```

2. 嵌套路由:

可以通过嵌套 `<router-view>` 来显示嵌套子路由：

```JavaScript
const router = new VueRouter({
	routes： [
		{ path: '/user/:id', component: User,
		  child: [
			  { path: 'profile', component: UserProfile },
			  { path: 'posts', component: UserPosts }
		  ]
		}
	]
})
```

3. 编程式导航：

除了使用 `<router-link>` 创建 a 标签来定义导航链接，我们还可以利用路由的实例方法：

```JavaScript
router.push('home');
router.push({ path: 'home' });
router.push({ name: 'path', params: { userId: '123' }});
```

4. 路由钩子：

可以利用钩子函数来实现在导航发生之前或之后执行的逻辑：

```JavaScript
const router = new VueRouter({
	routes: [
		{
			path: '/user/:id',
			component: User,
			beforeEnter：(to, from, next) => {
				// 路由独享的守卫
			}
		}
	]
});

router.beforeEach((to, from, next) => {
	// 全局前置守卫
});

router.afterEach((to, from) => {
	// 全局后置钩子
})
```

##### 实现原理

1. 路由注册：当我们创建一个 VueRouter 实例，并传入一个 `routes` 配置数组，`vue-router` 根据此配置创建一个路由映射表

2. 监听 URL 变化：在 Hash 模式下，通过监听 `hashchange` 事件；在 History 模式下，通过监听 `popstate` 事件

3. 注入：响应式地将当前路由对象 `$route` 注入到所有 Vue 实例，我们可以在任何组件内使用 `this.$route` 来访问当前路由对象，URL 的变化被自动捕获

4. `<router-view>` 渲染：基于当前的路由，`<router-view>` 组件递归渲染对应的组件
