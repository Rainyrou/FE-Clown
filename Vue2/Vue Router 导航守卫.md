Vue Router 导航守卫是一些通过跳转或取消的方式守卫导航的钩子。它们用于权限控制、路由跳转前的数据获取和异步验证等

1. 全局守卫:

   - `router.beforeEach`: 在导航触发前被全局调用
   - `router.afterEach`: 在导航触发后被全局调用
   - `router.beforeResolve`: 在解析导航前被全局调用

2. 路由独享守卫:

   - 在路由配置上定义的 `beforeEnter`

3. 组件内的守卫:

   - `beforeRouteEnter`: 组件被渲染前调用，不能获取组件实例 `this`
   - `beforeRouteUpdate`: 路由改变，但该组件被复用时调用
   - `beforeRouteLeave`: 导航离开该组件的对应路由时调用

###### 工作流程

1. 当用户点击链接或通过 `router.push()` 或 `router.replace()` 触发路由时，导航开始
2. 在失活的组件里调用 `beforeRouteLeave` 守卫，这允许你在离开页面之前执行逻辑，比如保存草稿、确认对话或取消当前导航
3. 调用全局的 `beforeEach` 守卫
4. 在复用的组件里调用 `beforeRouteUpdate` 守卫 (2.2+)
5. 在路由配置里调用 `beforeEnter`
6. 解析异步路由组件
7. 在被激活的组件里调用 `beforeRouteEnter`
8. 调用全局的 `beforeResolve` 守卫 (2.5+)
9. 如果所有守卫都通过了，没有调用任何的 `next(false)`，导航就被确认
10. 调用全局的 `afterEach` 钩子
11. 触发 DOM 更新来反映新路由的内容
12. 调用 `beforeRouteEnter` 守卫中传入 `next` 的回调函数，创建好的组件实例将作为回调函数的参数

`beforeRouteEnter` 是唯一一个不能获取到组件实例 `this` 的守卫，因为它在确认导航之前被调用，这时组件实例还未被创建。因此需要通过 `next` 回调来访问组件实例

导航守卫是异步解析执行的，整个导航过程可以等待守卫完成后继续，可以进行异步操作（如数据验证或获取数据）再决定导航的继续执行或中断
