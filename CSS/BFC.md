```
display: flex/grid/inline-block

position: absolute/fixed

float: left/right

overflow: auto/hidden
```

块格式化上下文是布局过程中盒模型的区域，也是浮动元素与其他元素交互的区域

1. BFC 是内部的盒子，在垂直方向上，一个接一个地放置
2. 垂直方向的距离由 `margin` 决定，属于同一个 BFC 的两个相邻盒子的 `margin` 会发生重叠

