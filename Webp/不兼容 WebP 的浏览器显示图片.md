1. HTML5 `picture`：允许为不同情况指定多个源图片，浏览器选择它支持的第一个图片格式进行加载

```HTML
<picture>
  <source srcset="image.webp" type="image/webp">
  <source srcset="image.jpg" type="image/jpeg">
  <img src="image.jpg" alt="Description" style="width: auto;">
</picture>
```

2. CSS `@supports` ：作为 CSS 背景的图片，可以通过 CSS `@supports` 规则来实现条件加载

```CSS
.background {
  background-image: url('fallback.jpg');
}

@supports (background-image: image/webp) {
  .background {
    background-image: url('image.webp');
  }
}
```

3. JavaScript 动态检测：使用 JavaScript 动态检测浏览器是否支持 WebP 格式，根据检测结果设置相应的图片格式

```JavaScript
const supportsWebp = (callback) => {
  let img = new Image();
  img.onload = () => callback(true);
  img.onerror = () => callback(false);
  img.src = " data:image/webp;base64,UklGRjoAAABXRUJQVlA4IC4AAACyAgCdASoCAAIALmk0mk0iIiIiIgBoSygABc6WWgAA/veff/0PP8bA//LwYAAA";
};

supportsWebp((supported) =>
  supported
    ? (document.getElementById("myImage").src = "image.webp")
    : (document.getElementById("myImage").src = "image.jpg")
);
```

4. 服务端用户代理检测：在服务端通过检查 HTTP 请求的 User-Agent 字段确定用户的浏览器类型和版本，根据是否支持 WebP 选择发送 WebP 格式图片

```JavaScript
exports.supportsWebP = function (browser) {
  const { headers = {} } = browser.ctx.req || {};
  return /chrome|firefox|edge|safari/i.test((headers['user-agent'] || '').toLowerCase());
};
```

5. 构建工具如 Vite 和 Webpack 有处理图片和自动添加兼容性解决方案的插件如 `responsive-loader` 和 `imagemin-webp`
