margin 重叠是指在某些情况下，两个垂直方向上相邻的块级元素之间的 `margin` 不会相加，而是选择其中的一个最大值作为实际的 `margin`。这通常出现在以下三种情况中：

1. 相邻兄弟元素的 margin 重叠：如果两个块级元素相邻，且没有任何内容，也没有padding、border 将它们分开，它们的垂直 `margin` 将重叠

```CSS
<div style="margin-bottom: 30px;">Element A</div>
<div style="margin-top: 20px;">Element B</div>
```

在上面的例子中，两个 div 元素之间的实际 `margin` 将是 30px（取两者中的较大值），而不是 50px

2. 父元素与第一个或最后一个子元素的 margin 重叠：如果一个块级元素没有顶部边框、顶部内边距，那么它的顶部 `margin` 和它的第一个子元素的顶部 `margin` 之间会发生重叠。同样的，如果它没有底部边框、底部内边距，它的底部 `margin` 和它的最后一个子元素的底部 `margin` 也会发生重叠

3. 空块级元素的 margin 重叠：如果在一个块级元素中没有边框、内边距、高度、最小高度、最大高度限制，并且它没有内联内容，那么该元素的顶部和底部 `margin` 将重叠

###### 解决方案

1. 使用 padding 或 border 为元素添加边框或内边距
2. 使元素浮动 `float: left/right` 
3. 触发 BFC。如设置 `overflow: auto/hidden` 可以创建一个新的 BFC
4. 使用 flexbox 或 grid 布局
5. 在两个元素之间添加一个 `display: inline-block` 的空 div
