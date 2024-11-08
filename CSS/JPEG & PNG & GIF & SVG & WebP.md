WebP 图片大小比 JPEG 或 PNG 小，但其压缩和解压缩过程更为复杂，浏览器解析 WebP 图片需更多的计算工作，因此在快网环境下其加载和渲染时间不如 JPEG 或 PNG 文件

1. Joint Photographic Experts Group：通过离散余弦变换和量化技术进行压缩，适用于需压缩以减少大小的彩色自然场景图片，传输大量图片，但有损压缩，不支持透明背景
2. Portable Network Graphics：通过 DEFLATE 压缩算法进行压缩，适用于 Logo 和高质量透明背景图片，无损压缩，支持透明背景，但不适合传输大量图片
3. Graphics Interchange Format：通过 Lempel-Ziv-Welch 压缩算法进行压缩，轻量级，无损压缩，支持动画，但支持 256 种颜色
4. Scalable Vector Graphics：基于 XML 的矢量图片格式，适用于 Logo 和动画，轻量级，无损压缩，支持无限缩放
5. WebP：通过 VP8 视频编码和 Riff 文件容器技术进行压缩，轻量级，无损压缩，支持动画和透明背景，压缩效率高于 JPEG 和 PNG，但兼容性较差，通过 HTML5 `picture` 指定多个源图片，浏览器选择其支持的首个图片格式加载

