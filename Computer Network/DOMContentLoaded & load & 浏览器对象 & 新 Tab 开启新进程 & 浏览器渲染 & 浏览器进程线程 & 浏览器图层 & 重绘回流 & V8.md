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

现代浏览器默认新 Tab 开启新进程，但存在同源进程复用优化，本质为浏览器多进程架构"隔离优先，优化兜底"的设计

- 早期浏览器为单进程架构，所有 Tab 共享同一进程，某 Tab 崩溃或恶意脚本执行则导致浏览器卡死和数据泄漏
- 现代浏览器为多进程架构，各个 Tab 独立运行于不同沙箱中，避免单点故障和数据泄露
- 当新 Tab 与现有 Tab 同源时，浏览器复用已有渲染进程，共享内存的公共资源

浏览器渲染：

- 主线程串行处理解析任务，HTML 解析器流式解析 HTML 字节流，将 HTML 标签拆分为 Token 并通过 DOM 构造器逐 Token 生成 DOM 树
- CSS 解析器解析 CSS 字节流，将其转换为结构化 CSS Token，再生成 CSS Object Model 树，样式引擎根据用户代理样式 -> 用户样式 -> 作者样式的优先级合并样式，通过 BloomFilter 过滤不匹配的 CSS 选择器分支，通过 Hash 过滤器查找匹配的 CSS 规则，最终根据继承规则、层叠上下文和特异性生成各个 DOM 节点的计算样式
- DOM 树和 CSS Object Model 树结合生成 Layout 树，具体来说，遍历 DOM 树为可见 DOM 节点创建 LayoutObject 并将 CSS Object Model 树的计算样式附着于 LayoutObject 上
- 浏览器遍历 Layout 树，基于盒模型布局算法，以包含块为单位，自上而下从左往右递归计算为各个 LayoutObject 的几何属性，Float/Position/Flex/Grid 等布局触发专属算法分支如 Flex 布局的主轴/交叉轴空间分配和 Grid 的网格轨道计算
- 布局计算基于脏值传播机制，若某 LayoutObject 的几何属性变化则标记其为脏节点并递归标记所有依赖它的父子节点，重新计算脏节点的页面布局
- 主线程遍历 Layout 树执行绘制操作，根据绘制顺序将各个 LayoutObject 转换为绘制指令，生成绘制记录
- 基于层叠上下文将 Layout 树拆分为绘制层，同一绘制层内的绘制指令根据顺序执行，不同绘制层的绘制相互独立
- 主线程将绘制记录和绘制层信息提交给合成器线程，合成器线程执行图层化，将绘制层拆分为默认图层和复合图层，构建图层树并计算各个图层的变换矩阵
- 光栅化线程通过分块光栅化策略将各个图层拆分为 256x256/512x512 瓦片，仅光栅化视口内和即将进入视口的瓦片，将绘制记录转换为像素数据，默认图层按需光栅化，复合图层的光栅化结果持久存储于 GPU 显存
- GPU 线程接收光栅化后的像素纹理，通过合成器管线执行图层合成，基于层叠上下文混合各复合图层纹理，应用变换和滤镜，最终生成帧缓冲区的像素数据，再通过显示控制器输出至屏幕
- 主线程的渲染任务遵循「JavaScript 执行 → 样式计算 → 布局绘制 → 图层合成」的单帧调度逻辑，任一环节耗时超过 16.6ms 触发丢帧；DOM/CSSOM 为非线程安全数据结构，主线程串行处理其修改/读取操作以避免竞态问题，而定时器/网络请求/GPU 任务由定时器/异步 HTTP 请求/合成器线程处理，完成后通过事件队列通知主线程更新

RenderLayer 渲染层（普通图层）：浏览器渲染流程中的第一层级，与 DOM 和 LayoutObject 一一对应，决定元素的层叠关系即 z 轴空间，其在 CPU 执行布局计算、绘制和合成等操作，通过根元素、定位属性、`opacity`、`transform`、`filter`、`mask`、`mix-blend-mode` 和 `overflow: hidden` 等触发层叠上下文
GraphicsLayer 图形层：浏览器生成最终图像的层级，其存储于共享内存，有独立的图形上下文，生成该层的位图并将位图作为纹理上传至 GPU 并参与最终绘制，此外非合成层与父层共享图形层
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

- 避免频繁读写触发重绘回流的 CSS 属性，可将其缓存
- 避免在循环中直接操作 CSS 或通过 `DocumentFragment` 批量更新
- 修改 CSS 类属性而非修改 `style` 属性
- 通过 `transform`、`opacity` 和 `requestAnimationFrame` 进行动画
- 避免使用 `table` 布局

浏览器为多进程的，操作系统为其进程分配 CPU 和内存等使其运行，每打开一个 Tab 页即创建一个独立的浏览器进程（在 Chrome 的 More tools 中打开任务管理器即可看到进程列表，由于 Chrome 的优化机制 Tab 页和进程一一对应的情况并不绝对如合并多个空白 Tab 页），多进程可避免单页面或第三方插件崩溃影响整个浏览器，同时充分利用多核 CPU

1. 浏览器主进程：用于显示用户界面，将渲染进程获取的内存之 Bitmap 绘制到用户界面上，与用户交互，管理各个页面和网络资源
2. 渲染进程（内核）：每打开一个 Tab 页即创建一个独立的浏览器渲染进程，用于渲染页面、处理事件和执行脚本
3. GPU 进程：用于 3D 绘制等
4. 第三方插件进程：插件和进程一一对应，仅在使用插件时才创建对应的第三方插件进程

![[Pasted image 20240502103023.png]]

浏览器主进程与渲染进程间通信：主进程接收到请求后首先获取页面内容，将该任务通过 RendererHost 接口传递给渲染进程，渲染进程接收并处理任务后，再传递给 GUI 渲染线程，渲染页面，再将渲染结果返回给渲染进程，渲染进程将其返回给主进程，主进程接收到渲染结果并将其绘制

![[Pasted image 20240502100855.png]]
