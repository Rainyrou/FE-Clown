1. Joint Photographic Experts Group：使用离散余弦变换和量化技术进行压缩，适用于需要压缩以减少大小的彩色和自然场景图片

优点：兼容性好，适合大量图片的网络传输
缺点：有损压缩，不支持透明背景

2. Portable Network Graphics：使用 DEFLATE 压缩算法进行压缩，适用于 logo 和高质量或透明背景的图片

优点：无损压缩，支持透明背景
缺点：比 JPEG 大，不适合大量图片的网络传输

3. Graphics Interchange Format：使用 Lempel-Ziv-Welch 压缩算法进行压缩，适用于动画

优点：轻量级，无损压缩，支持动画
缺点：仅支持 256 种颜色，不适合色彩丰富的图片

4. Scalable Vector Graphics：适用于 logo、图表和动画

优点：轻量级，无损压缩，基于 XML 的矢量图片格式，支持无限缩放而不失真

5. WebP：使用 VP8 视频编码和 Riff 文件容器技术进行压缩，可取代 JPEG、PNG 和 GIF

优点：轻量级，支持无损、有损压缩、透明背景和动画，压缩效率高于 JPEG 和 PNG
缺点：兼容性较差

###### 选择图片格式

1. 图片质量：无损压缩 or 有损压缩
2. 图片大小
3. 图片特性：透明背景 or 动画
4. 浏览器和设备兼容性

