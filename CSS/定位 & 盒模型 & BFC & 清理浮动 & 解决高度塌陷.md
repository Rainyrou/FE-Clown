- Static Positioning：默认定位方式，始终处于文档流给予的位置，其不脱离文档流，无法通过 `left`、`right`、`top`、`bottom` 和 `z-index` 来改变元素位置
- Absolute Positioning：元素相对于其最近的非 Static Positioning 祖先元素进行定位，其脱离文档流，不占据空间，即不影响其他元素的布局，可通过 `left`、`right`、`top`、`bottom` 和 `z-index` 来改变元素位置
- Relative Positioning：元素相对于其在文档流的初始位置进行定位，其不脱离文档流，可通过 `left`、`right`、`top`、`bottom` 和 `z-index` 来改变元素位置
- Fixed Positioning：元素相对于浏览器窗口进行定位，无论页面是否滚动始终处于固定位置，其脱离文档流，可通过 `left`、`right`、`top`、`bottom` 和 `z-index` 来改变元素位置
- Sticky Positioning：Relative Positioning + Fixed Positioning，其不脱离文档流，初始为 Relative Positioning，滚动到一定位置变为 Fixed Positioning，可通过 `left`、`right`、`top`、`bottom` 和 `z-index` 来改变元素位置

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

盒模型组成：

- Content：由 `width` 和 `height` 构成
- Padding：通过 `padding-top`、`padding-right`、`padding-bottom`、`padding-left` 或 `padding` 设置
- Border：通过 `border-width`、`border-style`、`border-color` 或 `border` 设置
- Margin：通过 `margin-top`、`margin-right`、`margin-bottom`、`margin-left` 或 `margin` 设置

盒模型种类：

- 标准盒模型：`width` 和 `height` 包括内容，不包括内边距和边框，计算总元素的宽度：`width + padding-left + padding-right + border-left + border-right`

- IE 盒模型 ：`width` 和 `height` 包括内容、内边距和边框，计算内容的宽度：`width - (padding-left + padding-right + border-left + border-right)`

CSS3 `box-sizing` 属性：

- `box-sizing: content-box` 标准盒模型
- `box-sizing: border-box;` IE 盒模型（默认）

BFC：独立渲染区域，定义内部块级元素的布局方式且不影响外部元素

- 内部元素垂直排列
- 内部相邻元素垂直方向 margin 重叠，非同一 BFC 的则互不影响

触发 BFC 的条件：

- 根元素
* display: flow-root/flex/grid/inline-block/table-cell/table-caption
* position: absolute/fixed
* float: left/right（非 none）
* overflow: auto/hidden/scroll（非 visible）

父元素未设置高度且子元素都设置为浮动元素时，父元素高度无法被子元素撑开（其高度计算为 0），则出现高度塌陷问题

- 为父元素触发 BFC
- 为父元素设置伪元素并为其设置 `clear: both`
- 在浮动元素后添加空 HTML 标签并为其设置 `clear: both`
