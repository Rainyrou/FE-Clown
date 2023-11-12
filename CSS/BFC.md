Block Formatting Context（块格式化上下文）是 Web 页面的可视化 CSS 渲染的一部分，是布局过程中盒模型的区域，也是浮动元素与其他元素交互的区域

1. BFC 是内部的盒子，在垂直方向上，一个接一个地放置
2. 垂直方向的距离由 `margin` 决定。属于同一个 BFC 的两个相邻盒子的 `margin` 会发生重叠
3. BFC 的区域不会与浮动元素盒子的区域重叠
4. BFC 是浮动元素所参照的区域
5. 计算高度时，浮动子元素也参与计算，这是清除浮动的一个原理

###### 触发 BFC 的条件

1. `float` 不为 `none`
2. `position` 为 `absolute` 或 `fixed`
3. `display` 为 `flex`、`grid`、`inline-block`、`table-cell`、`table-caption` 等
4. `overflow` 不为 `visible`
