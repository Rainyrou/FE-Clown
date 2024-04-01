在 HTML 中，`<img>` 标签的 `alt` 属性和 `title` 属性都可以提供关于图像的信息

`alt` 属性用于指定图像的替代文本

- 对于屏幕阅读器等辅助技术，`alt` 文本提供了图像描述，帮助视障用户理解图像内容
- 若出于某种原因图像无法显示，`alt` 文本会在图像位置显示

```HTML
<img src="path/to/image.jpg" alt="text describing the content of the image">
```

`title` 属性用于提供有关图像的附加信息，通常以 tooltip 的形式显示

* 当用户将鼠标悬停在图像上时，`title` 属性的内容将作为工具提示显示

```HTML
<img src="path/to/image.jpg" title="text to display on mouseover">
```
