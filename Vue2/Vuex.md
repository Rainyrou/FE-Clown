
![[Pasted image 20231108200514.png]]

- Vue Components：作为用户界面的构建模块，Vue 组件负责展示界面和响应用户输入，调用 dispatch 方法与 Vuex 进行交互，从而触发对应 actions 进行回应
- dispatch：触发 actions 的唯一方法
- actions：负责接收、处理 Vue Components 的一切交互行为，包含同步和异步操作，并支持多个同名方法，按照注册的顺序依次触发。向后台 API 请求的操作也在这个模块中进行，包括触发其他 actions 及通过 commit 提交 mutations。该模块提供了 Promise 的封装，以支持 action 的链式触发
- commit：触发 mutations 的唯一方法
- mutations：修改 state 的唯一方法，在严格模式下其他方式将会报错，它接收当前状态作为第一个参数。这种方法只能同步操作，且方法名全局唯一，这样 Vue DevTools 才能时刻追踪到状态变化。mutations 的方法应原子化，每个方法负责的工作单一明确
- state：Vuex 使用单一状态树，它集中存储 Vue Components 中 data 对象的零散数据，全局唯一，以进行统一的状态管理。从该对象中读取页面显示所需的数据，利用 Vue 的细粒度数据响应机制来进行高效的状态更新
- getters：基于 state 计算得出的值，类似于 Vue 的 computed。getters 可用于读取和监听全局 state 的变化，进而返回计算后的数据。这使得组件仅负责渲染和初始的事件响应，而不涉及复杂的状态逻辑。它在图中没有单独列出，其被包含在 render 中

Vue 组件调用 `dispatch` 方法触发 `actions` 来响应用户交互，`actions` 处理异步事件并通过 `commit` 提交 `mutations` 来同步修改 `state`。组件通过 `getters` 获取更新后的 `state` 数据，并依此来重新渲染 Vue 组件，更新用户界面