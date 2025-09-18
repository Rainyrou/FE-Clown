```
in -> static + relative + sticky(fixed + relative)

off -> absolute + fixed(window)

left/right/top/bottom -> absolute + relative + fixed + sticky

box-sizing: content-box + border-box

content/padding/border/margin

content-box: width/height = content

border-box: width/height = content + padding + border

display: flex/grid/inline-block

position: absolute/fixed

float: left/right

overflow: auto/hidden
```

1. Static Positioning：默认定位方式，始终处于文档流给予的位置，其不脱离文档流，无法通过 `left`、`right`、`top`、`bottom` 和 `z-index` 来改变元素位置
2. Absolute Positioning：元素相对于其最近的非 Static Positioning 祖先元素进行定位，其脱离文档流，不占据空间，即不影响其他元素的布局，可通过 `left`、`right`、`top`、`bottom` 和 `z-index` 来改变元素位置
3. Relative Positioning：元素相对于其在文档流的初始位置进行定位，其不脱离文档流，可通过 `left`、`right`、`top`、`bottom` 和 `z-index` 来改变元素位置
4. Fixed Positioning：元素相对于浏览器窗口进行定位，无论页面是否滚动始终处于固定位置，其脱离文档流，可通过 `left`、`right`、`top`、`bottom` 和 `z-index` 来改变元素位置
5. Sticky Positioning：Relative Positioning + Fixed Positioning，其不脱离文档流，初始为 Relative Positioning，滚动到一定位置变为 Fixed Positioning，可通过 `left`、`right`、`top`、`bottom` 和 `z-index` 来改变元素位置

```CSS
.static {
  position: static;
}

.relative {
  position: relative;
  top: 10px;
  left: 20px;
}

.absolute {
  position: absolute;
  top: 0;
  right: 0;
}

.fixed {
  position: fixed;
  bottom: 0;
  left: 0;
}

.sticky {
  position: sticky;
  top: 10px;
}
```

盒模型的组成部分包括：

1. Content：元素的实际内容，其尺寸由 `width` 和 `height` 属性决定
2. Padding：内边距是内容周围的空间，位于内容和边框之间。它可以增加元素的大小。使用 `padding-top`、`padding-right`、`padding-bottom`、`padding-left` 或简写属性 `padding` 来设置
3. Border：边框包围着内容和内边距，为元素定义了一个边界。它也可以增加元素的大小。使用 `border-width`、`border-style`、`border-color` 或简写属性 `border` 进来设置
4. Margin：外边距是元素外部的空间，位于边框和其他元素之间。它不会增加元素的实际大小，但会影响元素与其周围元素的距离。使用 `margin-top`、`margin-right`、`margin-bottom`、`margin-left` 或简写属性 `margin` 来设置

盒模型种类：

1. 标准盒模型 (Standard Box Model)

   - 在这种模型中，`width` 和 `height` 只设置内容的大小，不包括内边距和边框
   - 计算总元素的宽度：`width + padding-left + padding-right + border-left + border-right`

2. IE 盒模型 (Quirks Mode Box Model)

   - 在这种模型中，`width` 和 `height` 包括内容、内边距和边框
   - 计算内容的宽度：`width - (padding-left + padding-right + border-left + border-right)`

为避免浏览器的差异和混淆，CSS3 引入 `box-sizing` 属性。这使得开发者可以选择使用哪种盒模型：

- `box-sizing: content-box;` 代表标准盒模型
- `box-sizing: border-box;` 代表 IE 盒模型（默认）

块格式化上下文是布局过程中盒模型的区域，也是浮动元素与其他元素交互的区域

1. BFC 是内部的盒子，在垂直方向上，一个接一个地放置
2. 垂直方向的距离由 `margin` 决定，属于同一个 BFC 的两个相邻盒子的 `margin` 发生重叠

触发 BFC 的条件：

- display: flex/grid/inline-block
- position: absolute/fixed
- float: left/right
- overflow: auto/hidden

margin 重叠：垂直方向上相邻块级元素间的 `margin` 不相加，而以较大值为 `margin`

解决方案：

1. `padding` 或 `border` 
2. 触发 BFC
