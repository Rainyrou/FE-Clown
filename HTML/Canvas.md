HTML5 `<canvas>` 元素提供位图绘图接口，通过 JavaScript 动态渲染图形

渲染流程：在 HTML 中通过 `<canvas>` 标签创建画布元素，通过 JavaScript获取2D/WebGL渲染上下文，调用API绘制路径、形状和文本等，指令提交后，浏览器将绘图操作转化为位图数据，提交至CPU或GPU渲染，位图与其他页面元素合成，最终显示在页面上

1. 减少绘制区域：使用 `ctx.clearRect(x, y, w, h)` 局部清除而非全屏 `ctx.clearRect(0, 0, width, height)`
2. 避免频繁状态切换：批量绘制相同样式图形以减少重复设置 `fillStyle`、`strokeStyle`
3. 离屏Canvas双缓存
4. 分层渲染：使用多个Canvas叠加，分离动态与静态层如背景层和UI层
5. 将耗时计算移至Worker线程，利用 `OffscreenCanvas` 避免阻塞主线程
6. 硬件加速：通过3D变换触发GPU加速
7. Retina 屏幕模糊：根据 `devicePixelRatio` 缩放Canvas

```js
const scale = window.devicePixelRatio;
canvas.width = canvas.clientWidth * scale;
canvas.height = canvas.clientHeight * scale;
ctx.scale(scale, scale);
```