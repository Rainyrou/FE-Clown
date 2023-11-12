###### source

- `src` 用于在当前文档中嵌入资源。当浏览器解析到 `src` 属性时，它会立即下载该资源，并将其包含到文档中相应的位置上
- `<img>`, `<script>`, `<iframe>` 和 `<audio>` 等标签

例如，在 `<img>` 标签中使用 `src` 来指定图片的路径：

```HTML
<img src="image.jpg" alt="Description">
```

在 `<script>` 标签中使用 `src` 来指定外部 JavaScript 脚本：

```JavaScript
<script src="script.js"></script>
```

###### hypertext reference

- `href` 用于建立当前元素和引用资源之间的链接
- `<a>`, `<link>`, `<base>` 等标签。在 `<a>` 标签中，`href` 定义了一个超链接，当用户点击一个带有 `href` 属性的元素时，浏览器会导航到属性中指定的 URL。对于 `<link>` 和 `<base>`，浏览器会加载引用的资源，但不会导航到另一页面

例如，在 `<a>` 标签中使用 `href` 来创建一个到另一个页面的链接：

```HTML
<a href="https://www.example.com">Visit Example.com</a>
```

在 `<link>` 标签中使用 `href` 来关联一个 CSS 样式表：

```JavaScript
<link rel="stylesheet" href="styles.css">
```

简而言之，`src` 用于替换当前内容，而 `href` 用于建立当前元素和引用资源之间的链接。在处理它们时，浏览器的行为也有所不同，`src` 指向的内容会嵌入到文档中，而 `href` 指向的是一个将被浏览器访问的资源或位置
