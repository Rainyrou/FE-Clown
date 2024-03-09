1. 外部样式：通过 `<link>` 标签在 HTML 文档的 `<head>` 部分引入一个外部 CSS 文件，可以在多个页面之间共享 CSS 文件，浏览器可以缓存外部样式，但会增加页面的加载时间

```HTML
<link rel="stylesheet" href="styles.css">
```

2. 内部样式：通过 `<style>` 标签在 HTML 文档的 `<head>` 部分直接写入 CSS 规则，无需加载外部文件，但不利于样式复用

```HTML
<style>
	body {
	    background-color: red;
	}
</style>
```

3. 内联样式：通过元素的 `style` 属性直接在元素上写入 CSS 规则，定制化样式但不利于样式复用

```HTML
<div style="color: blue; font-size: 14px;">blue text.</div>
```

4. 导入样式表：使用 `@import` 规则在 CSS 文件内部引入另一个 CSS 文件，在 CSS 内部实现样式文件的模块化，但会增加页面的加载时间

```CSS
@import url("another-style.css");
```
