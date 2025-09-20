在 iOS Safari 和 WebView 中，当页面 HTML 元素字体大小小于 16px 或用户点击输入框时，页面自动放大

1. Meta Viewport

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```

- `maximum-scale=1.0`：禁止页面的最大缩放比例超过 1
- `user-scalable=no`：禁用用户缩放功能

2. 设置输入框默认字体大小

```css
input,
textarea {
    font-size: 16px;
}
```

3. JavaScript 动态设置，在聚焦时临时调整字体大小，不影响页面其他部分

```js
document.querySelectorAll("input, textarea").forEach((element) => {
  element.addEventListener("focus", () => (element.style.fontSize = "16px"));
  element.addEventListener("blur", () => (element.style.fontSize = ""));
});
```

