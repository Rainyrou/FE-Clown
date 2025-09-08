`src` 用于在 HTML 嵌入资源，当浏览器解析 `src` 属性时，其下载相应资源并将其包含至 HTML 相应位置上如 `<img>`、`<script>`、`<iframe>` 和 `<audio>` 等

```HTML
<img src="image.jpg" alt="Description">
```

```JavaScript
<script src="script.js"></script>
```

`href` 用于建立当前元素和引用资源间的链接如 `<a>`, `<link>`, `<base>` 等

```HTML
<a href="https://www.example.com">Visit Example.com</a>
```

```JavaScript
<link rel="stylesheet" href="styles.css">
```

`alt` 提供图片替代文本

```HTML
<img src="path/to/image.jpg" alt="text describing the content of the image">
```

`title` 提供图片附加信息，以 tip 形式显示

```HTML
<img src="path/to/image.jpg" title="text to display on mouseover">
```
