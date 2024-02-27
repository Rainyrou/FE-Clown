Positioning 允许你控制元素到页面某一精确位置上

###### 1. Static Positioning

- 默认定位方式
- 不脱离文档流
- 无法通过 `top`、`bottom`、`left`、`right` 和 `z-index` 来改变元素位置

###### 2. Absolute Positioning

- 元素相对于其最近的非 Static（Absolute & Relative & Fixed & Sticky）Positioning 祖先元素进行定位
- 脱离文档流
- 可以通过 `top`、`bottom`、`left`、`right` 来改变元素位置
- 不占据空间，即不影响其他元素的布局

###### 3. Relative Positioning

- 元素相对于其正常位置进行定位
- 不脱离文档流
- 可以通过 `top`、`bottom`、`left`、`right` 来改变元素位置
- 移动后仍保留剩余空间

###### 4. Fixed Positioning

- 元素相对于浏览器窗口进行定位
- 脱离文档流
- 可以通过 `top`、`bottom`、`left`、`right` 来改变元素位置
- 即使页面滚动，仍保持在固定的位置

###### 5. Sticky Positioning

- Fixed Positioning + Relative Positioning
- 不脱离文档流，直到滚动到一定位置时变成 Fixed Positioning
- 可以通过 `top`、`bottom`、`left`、`right` 来改变元素位置

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
