|       | preload                           | prefetch                      |
| ----- | --------------------------------- | ----------------------------- |
| 目标    | 当前页面关键渲染路径资源（首屏 CSS、动态加载的脚本和 SDK） | 预测页面可能用到的非关键资源（下一页和动态路由组件等）   |
| 加载时机  | 优先级高，立即加载，无需等待浏览器空闲时              | 优先级低，浏览器空闲时加载                 |
| 缓存储位置 | 存储于内存，随页面关闭释放                     | 存储于硬盘，跨页面存储，缓存时间由 HTTP 头部字段控制 |

```html
<link rel="preload" href="critical.css" as="style">
<link rel="preload" href="lazy-image.jpg" as="image">
<link rel="preload" href="analytics.js" as="script">
<link rel="prefetch" href="next-page.js" as="script">
<link rel="prefetch" href="product-modal.chunk.js" as="script">
```

主线程串行处理解析任务，HTML 解析器流式解析 HTML 生成 DOM 树，若遇到内联且无 async/defer 的 JavaScript 则立即停止 DOM 解析，加载并执行 JavaScript，有 async/defer 的 JavaScript 则与 DOM 解析并行加载执行，多个 async 的 JavaScript 分别在加载后立即执行，在 HTML 中的执行顺序无法保证，而多个 defer 的 JavaScript 则保持在 HTML 中的执行顺序

