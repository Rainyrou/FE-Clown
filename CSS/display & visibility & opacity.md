###### display: none

- 设置 `display: none` 时，元素从渲染树中移除，它和所有子元素均不被渲染，即它不会占据空间也不参与布局
- 可能触发 reflow 和 repaint
- 该元素上的 JavaScript 事件不被触发
- 显示隐藏元素时，可能导致 DOM 的重新计算

###### visibility: hidden

- 设置 `visibility: hidden` 时，元素及其所有子元素均不可见，但它们仍存在于渲染树中，占据空间并参与布局
- 只触发 repaint 而不触发 reflow
- 该元素上的 JavaScript 事件仍被触发

###### opacity: 0

- 设置 `opacity: 0` 时，元素及其所有子元素均不可见，但它们仍存在于渲染树中，占据空间并参与布局
- 该元素上的 JavaScript 事件仍会被触发
- 复合图层

