`display: none`：元素及其子元素从 Render Object 树中移除，不占据页面空间也不参与页面布局，隐藏元素时触发回流，同时由于其不存在于渲染树中，元素的 JavaScript 事件无法被触发
`visibility: hidden`：元素及其子元素均不可见但仍存在于 Render Object 树中，占据页面空间并参与页面布局，隐藏元素时触发重绘，同时由于其仍存在于渲染树中，元素的 JavaScript 事件仍被触发
`opacity: 0`：元素及其子元素均不可见但仍存在于 Render Object 树中，占据页面空间并参与页面布局，隐藏元素时触发重绘，同时由于其仍存在于渲染树中，元素的 JavaScript 事件仍被触发，此外 `opacity: 0` 还创建独立于默认文档流的复合图层
