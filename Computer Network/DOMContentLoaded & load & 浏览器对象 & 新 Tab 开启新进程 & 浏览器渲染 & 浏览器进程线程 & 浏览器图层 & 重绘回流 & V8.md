[https://segmentfault.com/a/1190000012925872](https://segmentfault.com/a/1190000012925872)

浏览器根据服务端返回响应头部 `Content-Type` 字段区分页面 & 图片

页面 -> `text/html`
图片 -> 图片格式如 `image/webp`、`image/png` 等

DOMContentLoaded/DOM Ready：HTML 被完全加载解析即 DOM 树构建完毕时触发，但不包括 CSS、图片和 iframe 等的加载
load：整个页面及其所有静态资源如 CSS、图片和 iframe 等均加载完毕时触发

```js
window.addEventListener("DOMContentLoaded", () => {
  const domReadyTime =
    performance.timing.domContentLoadedEventEnd -
    performance.timing.navigationStart;
  console.log(`${domReadyTime} ms`);
});

window.addEventListener("DOMContentLoaded", () => {
  const [navEntry] = performance.getEntriesByType("navigation");
  const domReadyTime = navEntry.domContentLoadedEventEnd;
  console.log(`${domReadyTime} ms`);
});
```

`navigationStart` -> 浏览器开始导航
`domContentLoadedEventEnd` -> DOMContentLoaded 事件触发完成

1. window：浏览器全局对象，作为变量和函数的全局作用域
2. screen：提供屏幕信息如宽高
3. document：当前在窗口中加载的文档，DOM 的入口点，提供与页面交互的属性和方法
4. localStorage & sessionStorage
5. XMLHttpRequest & Fetch
6. history
7. location：提供与当前 URL 交互的属性和方法
8. navigator：提供有关浏览器的信息如浏览器版本和操作系统

新 Tab 开启新进程：

* 现代浏览器默认新 Tab 开启新进程，但存在同站进程复用优化，本质为浏览器多进程架构下的"隔离优先、优化兜底"设计
* 早期浏览器为单进程架构，所有 Tab、JavaScript 执行、DOM 渲染和网络请求置于同一进程，一个 Tab 崩溃即导致整个浏览器卡死且恶意脚本可能通过进程共享窃取其他 Tab 数据，现代浏览器为多进程架构，通过进程隔离将不同功能模块拆分至独立进程，避免单点故障和数据泄露
* 各个 Tab 默认对应一个独立渲染进程，一个 Tab 崩溃导致当前渲染进程中断，而其他 Tab 不受影响，各个渲染进程运行于沙箱中，无法直接访问系统资源或其他进程，多进程通过 CPU 多核特性多并行处理多个 Tab 的渲染和 JavaScript 执行
* 同站（协议 + 域名 + 端口）进程复用优化即同站新 Tab 复用已有渲染进程，共享内存中的公共资源，减少内存占用

Chromium 118+采用单主线程 + 多协作线程模型

- 主线程串行处理解析任务，HTML 解析器流式解析 HTML 生成 DOM 树，解析 CSS 生成 CSS Object Model 树，若遇到内联且无 async/defer 的 JavaScript 则立即停止 DOM 解析，加载并执行 JavaScript，有 async/defer 的 JavaScript 则与 DOM 解析并行加载执行，多个 async 的 JavaScript 分别在加载后立即执行，在 HTML 中的执行顺序无法保证，而多个 defer 的 JavaScript 则保持在 HTML 中的执行顺序，而 CSS 加载虽不阻塞 DOM 的解析，但由于后续 Layout 树依赖于 CSS Object Model 树，因此 CSS 加载阻塞 Layout 树生成
- DOM 树和 CSS Object Model 树结合生成 Layout 树
- 浏览器遍历 Layout 树，通过 Box Layout Algorithm 为每个 LayoutObject 计算出其具体的位置和尺寸，这一过程包括级联计算（通过样式引擎合并继承样式、作者样式和用户代理样式）、规则匹配优化（通过 Hash 过滤器查找匹配的 CSS 规则且通过 BloomFilter 过滤不匹配的分支，根据层叠权重和继承规则生成最终样式）
- 布局计算完成后，主线程遍历 Layout 树执行绘制操作，将各个 LayoutObject 转换为绘制指令，生成绘制操作记录，并将其提交给合成器线程

```cpp
class LayoutObject {
  PhysicalRect FragmentsBoundingBox; // 支持 CSS Fragmentation
  const ComputedStyle* Style;        // 计算后样式（共享指针）
  PaintLayer* Layer;                 // 所属图层
  bool NeedsPaintInvalidation;       // 重绘标记
  LayoutObject* NextSibling;         // 树形结构指针
};
```

- 浏览器将默认图层和独立复合图层传递给 GPU，GPU 负责默认图层光栅化（即将绘制记录转换为像素数据）和复合图层纹理管理，合成器线程合并各个图层并生成最终图像
- JavaScript 执行、布局计算和绘制操作均在同一主线程上依次进行，避免多线程操作 DOM 时出现的竞态问题，但于此同时，定时器、网络和 GPU 等任务则由定时器线程、异步 HTTP 请求线程和 GPU 或其他线程处理，再通过事件队列通知主线程更新

RenderLayer 渲染层（普通图层）：浏览器渲染流程中的第一层级，与 DOM 和 LayoutObject 一一对应，决定元素的层叠关系即 z 轴空间，其在 CPU 执行布局计算、绘制和合成等操作，通过根元素、定位属性、`opacity`、`transform`、`filter`、`mask`、`mix-blend-mode` 和 `overflow: hidden` 等触发层叠上下文
GraphicsLayer 图形层：浏览器生成最终图像的层级，其存储于共享内存，拥有独立的图形上下文，生成该层的位图并将位图作为纹理上传至 GPU 并参与最终绘制，此外非合成层与父层共享图形层
CompositingLayer 合成层（复合图层）：为优化特定操作如动画效果而硬件加速即将某元素变为复合图层如主动触发即静态生效 + 强制创建 `transform(3D)/will-change/overflow: scroll/auto/iframe/video/canvas/backdrop-filter`、被动触发即条件触发 + 优化性能 `transform(2D)/opacity/filter/position: fixed/sticky/z-index` 等，在 GPU 中独立合成图像，文档流即为复合图层，开启硬件加速的元素则为另一复合图层

重绘：元素的外观变化但几何属性不变时触发重绘，浏览器需更新元素外观但无需重新计算元素的几何属性及页面布局，触发重绘的操作：修改 `color`、`background-color` 和 `visibility` 的值
回流：元素的几何属性变化时触发回流，浏览器需重新计算元素的几何属性及页面布局，触发回流的操作：设置 `style` 或 CSS 伪类，修改元素的几何属性如 `width`、`height`、`padding`、`border` 和 `margin`，增删可见 DOM 元素，刷新页面或修改视口

```js
div.style.left = div.offsetLeft + 1 + 'px';
div.style.top = div.offsetTop + 1 + 'px';
```

执行第一行代码：

a. 读取 `div.offsetLeft`：此时浏览器上没有未处理的样式修改（假设之前的代码没有改变任何样式），因此无需回流，直接返回当前值
b. 设置 `div.style.left` = 新值：此时样式修改，但浏览器并未立即回流，而将这一变化被加入到渲染队列，等待处理

执行第二行代码：

a. 读取 `div.offsetTop`：此时浏览器上有未处理的样式修改，因此立即触发一次回流以计算最新布局，确保`offsetTop` 的值最新
b. 设置 `div.style.top` = 新值：这一变化被加入到渲染队列，但此时即 JS 执行期间没有强制触发回流，当这段 JS 代码执行完毕，浏览器的渲染引擎在下一帧进行渲染时清空渲染队列，再次触发一次回流

###### 解决方案

* 避免频繁读取触发重绘回流的 CSS 属性，可将其缓存
* 避免在循环中直接操作 CSS 或通过 `DocumentFragment` 批量更新
* 修改 CSS 类属性而非直接修改 `style` 属性
* 避免使用 `table` 布局
* 通过 `transform`、`opacity` 和 `requestAnimationFrame` 进行动画

浏览器为多进程的，操作系统为其进程分配 CPU 和内存等使其运行，每打开一个 Tab 页即创建一个独立的浏览器进程（在 Chrome 的 More tools 中打开任务管理器即可看到进程列表，由于 Chrome 的优化机制 Tab 页和进程一一对应的情况并不绝对如合并多个空白 Tab 页），多进程可避免单页面或第三方插件崩溃影响整个浏览器，同时充分利用多核 CPU

1. 浏览器主进程：用于显示用户界面，将渲染进程获取的内存之 Bitmap 绘制到用户界面上，与用户交互，管理各个页面和网络资源
2. 渲染进程（内核）：每打开一个 Tab 页即创建一个独立的浏览器渲染进程，用于渲染页面、处理事件和执行脚本
3. GPU 进程：用于 3D 绘制等
4. 第三方插件进程：插件和进程一一对应，仅在使用插件时才创建对应的第三方插件进程

![[Pasted image 20240502103023.png]]

浏览器主进程与渲染进程间通信：主进程接收到请求后首先获取页面内容，将该任务通过 RendererHost 接口传递给渲染进程，渲染进程接收并处理任务后，再传递给 GUI 渲染线程，渲染页面，再将渲染结果返回给渲染进程，渲染进程将其返回给主进程，主进程接收到渲染结果并将其绘制

![[Pasted image 20240502100855.png]]
