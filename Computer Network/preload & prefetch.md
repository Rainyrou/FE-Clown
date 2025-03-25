|            | preload                                                    | prefetch                                               |
| ---------- | ---------------------------------------------------------- | ------------------------------------------------------ |
| 目标       | 当前页面关键渲染路径资源（首屏 CSS、动态加载的脚本和 SDK） | 预测页面可能用到的非关键资源（下一页和动态路由组件等） |
| 加载时机   | 优先级高，立即加载，无需等待浏览器空闲时                   | 优先级低，浏览器空闲时加载                             |
| 缓存储位置 | 存储于内存，随页面关闭释放                                 | 存储于硬盘，跨页面存储，缓存时间由 HTTP 头部字段控制   |

```html
<link rel="preload" href="critical.css" as="style">
<link rel="preload" href="lazy-image.jpg" as="image">
<link rel="preload" href="analytics.js" as="script">
<link rel="prefetch" href="next-page.js" as="script">
<link rel="prefetch" href="product-modal.chunk.js" as="script">
```
