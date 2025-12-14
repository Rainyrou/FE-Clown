表单增强：input 类型（`time` 时间选择器 + `date` 日期选择器 + `color` 颜色选择器 + `number` 数字输入框 + `url` URL 输入框 + `tel` 电话号码输入框 + `email` 电子邮件输入框）、表单属性（`required` 字段必填 + `multiple` 是否多值 + `placeholder` 输入为空时显示的文字 + `autocomplete` 输入是否自动完成 + `autofocus` 输入是否自动聚焦）
多媒体支持：`<audio>` 音频 + `<video>` 视频 + `<source>` 多媒体资源路径
Canvas & SVG
Drag & Drop API
Geolocation：获取用户地理位置信息
WebSocket & Web Worker & Web Storage

语义化：根据内容选择 HTML 标签， 语义化标签遵循 W3C 标准，有更好的兼容性，使 HTML 结构清晰（即便在没有 CSS 样式的情况下），提升 SEO 效果，便于爬虫

自定义属性存储额外信息，无任何视觉效果，以 `data-` 为前缀

元素根据显示方式分为块级元素和行内元素，通过 `display` 属性改变元素的默认行为

块级元素：用于创建页面结构，可包含块级/行内元素，在盒模型中设置 `width`、`height`、`padding` 和 `margin`，默认情况下占据其父容器的全部宽度，总是新起一行，随后的元素另起一行
行内元素：用于包裹页面文本，可包含行内元素，在盒模型中无法设置 `width` 和 `height`，按行内顺序依次排列至无法放置后另起一行
