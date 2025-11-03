`src` 嵌入资源，加载外部资源到文档指定位置，嵌入的资源为文档必要资源，阻塞HTML解析，如 `script/iframe/img/audio/video`

`href` 关联资源，定义文档与外部资源的关系，关联的资源为文档辅助资源，不阻塞HTML解析，如 `link/a/base`

`alt` 媒体资源的替代文本，无障碍阅读并为加载失败兜底，对图片SEO友好，无实际内容图片设置 `alt=""`，有实际内容图片设置有意义的 `alt`

`title` 页面元素的补充说明文本，鼠标悬浮提示，

`<canvas>` 元素提供位图绘图接口，通过  `<canvas>`  标签创建画布元素，通过 JavaScript 获取 2D/WebGL 渲染上下文，调用 API 绘制路径、形状和文本等，指令提交后，浏览器将绘图操作转化为位图数据，提交至 CPU 或 GPU 渲染，位图与其他页面元素合成，最终显示在页面上

1. 减少绘制区域：通过 `ctx.clearRect(x, y, w, h)` 局部清除而非全屏 `ctx.clearRect(0, 0, width, height)`
2. 避免频繁状态切换：批量绘制相同样式图形以减少重复设置 `fillStyle`、`strokeStyle`
3. 离屏 Canvas 双缓存
4. 分层渲染：通过多个 Canvas 叠加，分离动态与静态层如背景层和 UI 层
5. 将耗时计算移至 Worker 线程，通过 `OffscreenCanvas` 避免阻塞主线程
6. 硬件加速：通过 3D 变换触发 GPU 加速
7. Retina 屏幕模糊：根据  `devicePixelRatio`  缩放 Canvas

```js
const scale = window.devicePixelRatio;
canvas.width = canvas.clientWidth * scale;
canvas.height = canvas.clientHeight * scale;
ctx.scale(scale, scale);
```
