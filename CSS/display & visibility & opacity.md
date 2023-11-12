`display: none`、`visibility: hidden` 和 `opacity: 0` 都可以使元素不可见，但它们在工作原理和应用效果上有很大的区别

###### display: none

- 当为元素设置 `display: none` 时，该元素会被完全从渲染树中移除，该元素及其所有子元素都不会被渲染,即它不会占据空间也不参与布局
- 可能会触发 reflow 和 repaint
- 该元素上的 JavaScript 事件不会被触发
- 隐藏或显示元素时，可能会导致 DOM 的重新计算

```CSS
.hidden {
    display: none;
}
```

###### visibility: hidden

- 当为元素设置 `visibility: hidden` 时，该元素及其所有子元素都不可见，但它们仍存在于渲染树中，仍占据空间并参与布局，其位置仍会为后续元素所保留
- 通常只会触发 repaint 而不会触发 reflow
- 该元素上的 JavaScript 事件仍会被触发

```CSS
.invisible {
    visibility: hidden;
}
```

###### opacity: 0

- 当为元素设置 `opacity: 0` 时，该元素及其所有子元素都不可见，但仍占据空间并参与布局，其位置仍会为后续元素所保留
- 该元素上的 JavaScript 事件仍会被触发
- `opacity` 的变化可能会触发 GPU 加速的合成层 (compositing layer)，特别是当与硬件加速相关的属性如 `transform`、`filter` 等一起使用时

```CSS
.transparent {
    opacity: 0;
}
```

