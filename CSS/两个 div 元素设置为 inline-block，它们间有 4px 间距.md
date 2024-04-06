当两个 `div` 元素设置为 `inline-block` 时，它们间有 4px 的间距，因为 `inline-block` 元素间的空白字符在 HTML 中基于 HTML 的空白符合并规则被渲染成一个空格，该空格在渲染时产生额外间距

1. 移除 HTML 中的空白字符，确保 `inline-block` 元素在 HTML 代码中无空格、换行或制表符分隔

```html
<div></div><div></div>
```

2. 在元素间添加 HTML 注释阻止空白字符渲染

```html
<div></div><!----><div></div>
```

3. 为第二元素设置负的 `margin` 值抵消间距

```css
div + div {
  margin-left: -4px;
}
```

4. 父元素设置 `font-size: 0;`，重新为 `inline-block` 元素指定 `font-size`

```css
.container {
  font-size: 0;
}
.container div {
  font-size: 16px;
}
```
