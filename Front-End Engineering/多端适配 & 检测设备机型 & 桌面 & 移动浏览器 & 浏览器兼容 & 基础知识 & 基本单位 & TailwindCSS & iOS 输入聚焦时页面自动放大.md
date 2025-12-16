###### 多端适配

1. flexable.js（废弃）/Rem & JavaScript 根据 `window.devicePixelRatio/navigator.userAgent` 等判断设备机型/操作系统/浏览器内核动态条件渲染

```js
const setRemUnit = () => {
  const width = document.documentElement.clientWidth || window.innerWidth;
  const designWidth = 1440; // 设计稿基准宽度 1440px
  const baseFontSize = 100; // 1440px 宽度下，1 rem 对应实际大小为 100px
  const rootFontSize = (width / designWidth) * baseFontSize; // 计算根元素的 font-size
  document.documentElement.style.fontSize = `${rootFontSize}px`;
}

setRemUnit();
window.addEventListener('resize', setRemUnit);
```

2. Meta Viewport

```html
<meta name="viewport" content="width=device-width; initial-scale=1; maximum-scale=1; minimum-scale=1; user-scalable=no;">
```

Ideal Viewport：页面在移动端展示的理想大小，在浏览器调试移动端时页面给定的像素大小即理想视口大小，单位为设备独立像素，通过 `screen.width` 和 `screen.height` 获取

布局视口和视觉视口等于理想视口，`device-width` 即理想视口宽度，因此设置 `width=device-width` 即布局视口等于理想视口，而 `initial-scale` = 理想视口宽度 / 视觉视口宽度，因此设置为 1，1 个 CSS 像素等于 1 个设备独立像素

3. vh & vw & Flex 弹性布局 & Grid 网格布局 & 响应式图片 & 跨域字体

即将视觉视口宽度 `window.innerWidth` 和高度 `window.innerHeight` 等分为 100 份，借助 PostCSS 的 `postcss-px-to-viewport` 插件来完成布局计算

![[Pasted image 20241015142400.png]]

通过  `srcset`/`sizes`  或  `<picture>`  标签，根据设备像素比例或视口宽度自动加载对应分辨率的图片

跨域字体：

- 服务端配置 CORS，为其字体文件资源响应设置  `Access-Control-Allow-Origin`  头部字段
- 字体转换为 Base64 格式
- 通过  `new FontFace`  构造字体对象，通过  `crossOrigin: 'anonymous'`  让浏览器允许其匿名跨域加载，再将该字体对象添加至文档中

高清适配：

- 内部分辨率 = CSS 宽高 × 物理像素比：

```js
const scale = window.devicePixelRatio || 1;
canvas.width = cssWidth * scale;
canvas.height = cssHeight * scale;
const ctx = canvas.getContext('2d');
ctx.scale(scale, scale);
```

4. 媒体查询

- 设置根元素 `font-size`
- 屏幕宽高 `width` 和 `height` & 设备宽高 `device-width` 和 `device-height`
- 横竖方向 `orientation`
- 纵横比 `aspect-ratio`
- 分辨率 `resolution`
- 颜色 `color`
- 打印 `print

5. 跨端框架/H5 同构：从最早的 Web + Native 的混合架构，到 React Native 以 JSRuntime 驱动 Native 组件渲染的高性能跨端架构，再到 Flutter 以自绘能力推进多端一致性和性能显著提升的新架构尝试，大前端上层以类前端技术为研发基础 JavaScipt/TypeScript/Dart，下层通过抹平各类操作系统的 UI 渲染和底层 API 的能力差异形成端侧容器，该类架构具备一次开发多端交付的特点，在具备高性能的前提下拥有较好的动态部署能力

![[Pasted image 20241018173346.png]]

前端/客户端各自开发和维护一套，但工作量翻倍且无法保证前端和客户端代码架构和实现上的一致性，后续迭代容易出现分叉等问题，而一套代码 iOS/Android/H5 三端运行，通过 React Native 为客户端提供动态化能力并同构生成微信 H5，三端代码一致，告别三端独立开发或兼容适配，定制实现组件库，封装颜色 token、屏幕适配和夜间模式等，减少样式代码，将 React Native 的开发门槛从客户端降低为 Web

初期在浏览器以 Web 开发形式编写 UI、业务逻辑和接口联调等，无需启动模拟器或连接真机

- Chrome 选择 iPhone SE 宽度 375 直接与设计稿 1：1 对照
* 通过 React Devtool 查看页面内部 State、Store 和 Hooks 等数据
* 在 Chrome Network 调试 Tab 进行接口联调

![[1849e7ae7b9535ec3fe4d1af.mp4]]

中后期可三端同步开发

- 开启 Terminal 分别执行 React Native 调试命令 yarn dev 和 Web 调试命令 yarn web-dev
- 连接 iOS、Android 和 Web，一次改动三端页面同步热更新

![[1849e752b0652d523fe1b6c9.mp4]]

![[Pasted image 20241018153021.png]]

通过三端同构组件库和样式组件系统实现 React Native 业务代码的快速编写

- 客户端部分：通过 React Native 生成双端代码并为 App 提供动态化能力，同构页面在 APP 中打开速度媲美 Native，实现热更新，同时借助 RN/H5 同构改善 React Native 开发体验
- Web 部分：通过 React Native Web 将 React Native 代码编译为 H5 并实现同步部署和服务端渲染，将原先客户端才有的 React Native 之功能和体验完整复刻同步到微信体系，不像 Flutter Web 那样动辄几 MB 的 JavaScript 资源，可在生产环境投入使用，同时赋能业务，产品方便于微信上分享，用户可通过 H5 实现于 APP 端一致的业务流程

![[Pasted image 20241018153339.png]]

React Native Web 由前 X 前端主管 Necolas 开发并以此技术方案于 2017 年重构 X Web App 并沿用至今，React Native Web 在国内知名度不高，但放眼全球 Twitter Web、React Native、Expo 和 NativeBase 官网等采用此方案

![[Pasted image 20241018160017.png]]

同构改造：

- 在已有的 React Native 项目里引入 React Native Web 并选取 Next.js 作为服务端渲染的项目脚手架，通过 SSR 解决 React Native Web css-in-js 方案的首屏加载问题，Next.js 服务端渲染前端代码，自定义 Server 实现服务端代码，复用集团 Node 中间件
- 客户端 APP 为各个 React Native 页面注册 URL 路由，规则通过 JSON 动态下发，客户端读取后将 React Native 页面注册到 URL 路由上，进行匹配和跳转且控制 H5、React Native 和 Native 的优先级。同构 H5 路由与 React Native 路由相同，实现同一 URL 在客户端内打开 React Native 页面，在微信中打开 H5 页面，RN/H5 路由一致，H5 为客户端 React Native 页面兜底如 React Native 未覆盖到低版本客户端或 React Native 包出现问题
- 兼容代码处理 React Native/H5 差异，判断平台 OS  _:_  "iOS" | "android" | "windows" | "macos" | "web" | "node"，如在客户端打包时无需引入微信分享的 JSSDK，无需在 Node 执行端上逻辑

```js
if (OS === "web") wx = require("weixin-js-sdk");
if (OS === "node") useStaticRendering(true);
```

- Metro 处理 React Native/H5 差异，通过由 React Native Metro 提供的 native.js 后缀区分同构写法，JSBridge 在客户端调用 React Native 方法， 在 Web 端调用 Web 方法

```
RNBridge
├── index.js # 由前端构建工具打包的文件
├── index.native.js # 由 React Native Metro 打包的文件
```

- Next.js 处理 React Native/H5 差异，由于 Next.js 自动注册路由，服务端根据 pages 目录结构，生成对应 URL 路由，而默认 React Native 项目页面目录也为 pages，因此 Next.js 打包 pages 中的所有页面，需控制仅打包同构页面，因此为其添加自定义标识 `.web.js`，设置 Next.js 只处理 `web.js` 后缀的文件作为入口文件，实现与 React Native 代码共存

```js
module.exports = {
  pageExtensions: ["web.jsx", "web.js", "web.tsx", "web.ts"],
};
```

同一目录下的 React Native 入口与同构入口：

```
.
├── index.js
├── index.web.js
```

同构生成的复杂 H5 页面在低端机型上加载较慢，LightHouse 跑分证实词典，部分原因是 React Native Web 样式解析采用 css-in-js 方案，因此采用 SSR 服务端渲染并修改 Web 屏幕适配逻辑

- CICD 流程：由 Gitlab CI 实现三端同构的 CI 包括 commit 校验、代码 lint 校验、单元测试和 React Native 打包上传，由 Gitlab CI + 自研平台实现端侧 React Native 发布，所有 feature 分支 PR 合并后自动打 React Native 包，实现 RN 发布客户端版本控制、多阶段控制、代码上传和分包下发等

![[Pasted image 20241018163240.png]]

![[Pasted image 20241018163709.png]]

![[Pasted image 20241018163837.png]]

![[Pasted image 20241018163930.png]]

###### 检测设备机型

1. `navigator.userAgent` 字符串检测 + 服务端响应头部字段 `User-Agent`
2. 通过物理像素比二次验证：PC === 1 + 1 <= Pad <= 2 + 手机 >= 2

```js
const screenWidth = window.screen.width;
const screenHeight = window.screen.height;
const aspectRatio = screenWidth / screenHeight;

// 获取设备像素比
const devicePixelRatio = window.devicePixelRatio;
```

3. 媒体查询：PC > 1024px + 768px <= Pad <= 1024px + 手机 < 768px

```js
@media (max-width: 767px) {
  .content { padding: 10px; }
}

@media (min-width: 768px) and (max-width: 1024px) {
  .content { padding: 20px; }
}

@media (min-width: 1025px) {
  .content { padding: 30px; }
}
```

4. 通过 `ontouchstart` 属性或 `navigator.maxTouchPoints` 检测触控支持
5. 第三方库：react-device-detect + mobile-detect.js

###### 检测桌面 & 移动浏览器

参考文章：

[javascript - How to detect Safari, Chrome, IE, Firefox and Opera browsers? - Stack Overflow](https://stackoverflow.com/questions/9847580/how-to-detect-safari-chrome-ie-firefox-and-opera-browsers)

[JavaScript 判断桌面浏览器和移动浏览器](https://juejin.cn/post/6844903493938102279?searchId=20241125094003D167680F14FD190ABE48)

浏览器指纹：通过客户端信息来识别用户浏览器特性和运行环境，其包含操作系统及版本、设备类型、浏览器内核、浏览器语言和浏览器插件等信息，主要依赖于 `navigator.userAgent` 及其他浏览器提供的特性来获取环境数据

根据 `navigator.userAgent` 属性，以 `indexOf` 和正则匹配

先前的方法依赖于渲染引擎属性 `-moz-box-sizing`  和  `-webkit-transform` 及有欺骗性的 `navigator.userAgent` 属性判断浏览器，为更为可靠的判断，国外大佬通过浏览器特定属性判断：

Chrome：全局 `chrome`  对象包含多个属性如 `chrome.webstore`
Opera：`window.opera`  已存在多年，但当 Opera 将其引擎替换为 Blink + V8 时被废弃，Opera 15 的 UA 字符串与 Chrome 类似，但增加 `OPR`，而 `!!window.opr && opr.addons` 用于检测 Opera 20+
Firefox：API 安装附加组件 `InstallTrigger`
Safari：通过构造函数命名正则匹配，但在 Safari 9.1.3 中修复，因此通过 7.1 版本后引入的  `SafariRemoteNotification` 以涵盖 3.0 及更高版本的 Safari
Internet Explorer：JavaScript 的条件编译（IE9 前）和  `document.documentMode`
Edge ：在 Trident 和 Edge 浏览器中，Microsoft 的实现有 `StyleMedia`  构造函数，而排除 Trident 后只剩下 Edge
Edge（基于 Chromium）：`navigator.userAgent` 在末尾包含 `Edg/[version]`

```
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.16 Safari/537.36 Edg/80.0.361.9
```

- 判断桌面端还是移动端以显示对应主题和内容
- 判断判断 Android 或 iOS 引导用户下载对应的 App
- 判断微信浏览器还是 H5 浏览器调用不同的分享功能
- 获取浏览器内核和载体以统计设备平台分布，优化兼容性
- 获取浏览器版本提示用户更新浏览器

```js
class checkBrowser {
  constructor() {
    this.userAgent = navigator.userAgent;
    this.Android =
      this.userAgent.indexOf("Android") > -1 ||
      this.userAgent.indexOf("Linux") > -1;
    this.IPhone = this.userAgent.indexOf("iPhone") != -1;
    this.Ios =
      this.userAgent.indexOf("iPhone") > -1 ||
      this.userAgent.indexOf("Mac") > -1;
    this.Ipad = this.userAgent.indexOf("iPad") > -1;
    this.Opera = this.userAgent.indexOf("Opera") > -1;
    this.IE =
      this.userAgent.indexOf("compatible") > -1 &&
      this.userAgent.indexOf("MSIE") > -1 &&
      !this.Opera;
    this.Edge = this.userAgent.indexOf("Edge") > -1;
    this.FireFox = this.userAgent.indexOf("Firefox") > -1;
    this.Safari =
      this.userAgent.indexOf("Safari") > -1 &&
      this.userAgent.indexOf("Chrome") == -1;
    this.Chrome =
      !this.Edge &&
      this.userAgent.indexOf("Chrome") > -1 &&
      this.userAgent.indexOf("Safari") > -1;
    this.IE11 =
      this.userAgent.indexOf("Trident") > -1 &&
      this.userAgent.indexOf("rv:11.0") > -1;
  }
}
```

```js
// Opera 8.0+
var isOpera =
  (!!window.opr && !!opr.addons) ||
  !!window.opera ||
  navigator.userAgent.indexOf(" OPR/") >= 0;

// Firefox 1.0+
var isFirefox = typeof InstallTrigger !== "undefined";

// Safari 3.0+ "[object HTMLElementConstructor]"
var isSafari =
  /constructor/i.test(window.HTMLElement) ||
  (function (p) {
    return p.toString() === "[object SafariRemoteNotification]";
  })(
    !window["safari"] ||
      (typeof safari !== "undefined" && window["safari"].pushNotification)
  );

// Internet Explorer 6-11
var isIE = /*@cc_on!@*/ false || !!document.documentMode;

// Edge 20+
var isEdge = !isIE && !!window.StyleMedia;

// Chrome 1 - 79
var isChrome =
  !!window.chrome && (!!window.chrome.webstore || !!window.chrome.runtime);

// Edge (based on chromium) detection
var isEdgeChromium = isChrome && navigator.userAgent.indexOf("Edg") != -1;

// Blink engine detection
var isBlink = (isChrome || isOpera) && !!window.CSS;

var output = "Detecting browsers by ducktyping:<hr>";
output += "isFirefox: " + isFirefox + "<br>";
output += "isChrome: " + isChrome + "<br>";
output += "isSafari: " + isSafari + "<br>";
output += "isOpera: " + isOpera + "<br>";
output += "isIE: " + isIE + "<br>";
output += "isEdge: " + isEdge + "<br>";
output += "isEdgeChromium: " + isEdgeChromium + "<br>";
output += "isBlink: " + isBlink + "<br>";
document.body.innerHTML = output;
```

###### 浏览器兼容

**遇到浏览器兼容问题直接在 W3C 搜索，时间投入不宜过多**
**什么？你们公司需要兼容 IE6，那今天面试到此为止**

主流浏览器：Chrome（Blink 内核）、Opera（Blink 内核）、Firefox（Gecko 内核）、Safari（Webkit 内核）和 IE（Trident 内核）

W3C 标准的推进及 Chrome、Opera、Firefox、Safari 的出现结束 IE 统一天下的日子，浏览器厂商为争取更多市场，促成自家浏览器更符合 W3C 标准且扩展其功能如 `-webkit` 开头的样式，不同浏览器在处理同一页面时表现可能有所差异，此种差异可能过小，甚至不被注意到，可能过大，导致页面在某浏览器下无法正常浏览，引起这些差异的问题即浏览器兼容性问题，这是因为浏览器所用内核和标准不同且在处理同一事件的实现思路有所差异，即便同一浏览器，旧版本的 Bug 相对较多，对新标签、新属性和新特性支持度较小，而开发人员书写的不规范代码加重上述问题

从浏览器内核的角度来看，其兼容性问题可分为：

- 渲染：与样式相关的问题
- 脚本：与 JavaScript、BOM 和 DOM 相关的问题
- 其他：浏览器自身提供的功能，于内核层之上

不规范的嵌套：

```html
<div>
   <li>新闻标题一</li>
   <li>新闻标题二</li>
   <li>新闻标题三</li>
</div>
```

根据 HTML 标准，`<li>` 元素应嵌套于 `<ul>` 或 `<ol>` 元素中而不应直接置于 `<div>`，上述代码不符合 W3C 标准，HTML 标签嵌套有严格的规则，其保证文档结构的一致性，若不遵守这些规则，浏览器根据自身实现修复或纠正这些错误但可能导致渲染不一致的意外行为，如遇上述问题，浏览器自动将这些 `<li>` 元素包裹于一个隐式 `<ul>` 元素中，但旧版浏览器可能忽略 `<li>` 元素，导致页面渲染效果不合预期

不规范的 DOM 接口和属性设置：

```js
document.all.a_name.style.top = 35;
```

`style.top = 35` 不符合 CSS 语法，`style.top` 应为一个带有单位的值，而 `document.all` 在符合 HTML 的 DOM 标准之现代浏览器中已不推荐或支持，但其仍存在于早期的 IE 浏览器中，现代浏览器更倾向于通过 `document.getElementById` 或 `document.querySelector` 来获取元素

- 人为：浏览器本身 Bug、开发人员粗心大意或未对 W3C 标准有深入了解

在早期的 IE 浏览器中，`alt` 属性未按照标准处理工具提示，因此开发人员为兼容 IE，可能选择 `alt` 属性做鼠标悬停于 `img` 元素上方时出现提示信息的功能，但 W3C 标准规定去做上述事情的属性为 `title`，现代大多浏览器符合此标准，而早期 IE 不符合，这是 IE 浏览器内核的问题，而开发者书写非标准的代码，这是开发者的问题

处理兼容性问题的思路：

- Do or Not：产品用户浏览器比例/渲染效果优先 or 实现基本功能优先/有无必要
- 程度：哪些浏览器支持哪些效果
- 技术栈/第三方库/兼容工具/条件注释
- 渐进式增强/降级方案

JavaScript 兼容：

1. const

原因：在 Firefox 中可使用  `const`  和  `var`  定义常量但在 IE 中只能使用 `var`  定义常量
解决方案：统一使用 `var`  定义常量

2. 对象宽高赋值

原因：在 Firefox 中，直接通过 JavaScript 给元素 `style` 属性赋值是无效的如 `obj.style.height = imgObj.height`，直接将整数赋给 `style.height`，CSS 无法自动推断单位，而 Firefox 无法修复和纠正上述问题，必须显式提供单位
解决方案：在赋值时给 `height` 和 `width` 等属性加上单位

```js
obj.style.height = imgObj.height + "px";
```

3. 设置类名

原因：`setAttribute("style", "styleClass")` 在早期的 IE 浏览器中不支持
解决方案：

```js
object.className = "styleClass";
setAttribute("className", "styleClass");
```

4. 设置事件

```js
var obj = document.getElementById("objId");
obj.setAttribute("onclick", "functionname()");
```

原因：在 IE 中，上述方式无效，在早期的 IE 中，事件处理程序并不能像在标准 DOM 中那样直接作为 `onclick` 属性存在，其通过 `obj.attachEvent` 或点记法 `obj.onclick` 来绑定事件
解决方案：IE 必须通过点记法来绑定事件即以 `obj.onclick` 代替 `setAttribute("onclick", ...)` 且通过匿名函数传递事件处理程序

```js
var obj = document.getElementById("objId");
obj.onclick = function() {
   functionname();
};
```

5. innerText

原因：FireFox 不支持 `innerText`，由于 `textContent` 是 W3C DOM 规范的一部分，其不依赖于浏览器的特殊实现，在所有支持 DOM 标准的浏览器中均能正确工作，而 `innerText` 在非 IE 浏览器中并不普遍支持
解决方案：在非 IE 中使用 `textContent` 代替 `innerText`

```js
if (navigator.appName.indexOf("Explorer") > -1)
  document.getElementById("element").innerText = "my text";
else document.getElementById("element").textContent = "my text";
```

6. addEventListener & attachEvent

原因：`addEventListener` 为标准的事件绑定方法，符合 W3C DOM 标准，支持 Chrome、Opera、Firefox、Safari 、IE9 及更新版本，而 `attachEvent` 为 IE 特有的事件绑定方法，支持 IE8 及更早版本
解决方案：

```js
function addEvent(elm, evType, fn, useCapture) {
  if (elm.addEventListener) {
    elm.addEventListener(evType, fn, useCapture);
    return true;
  } else if (elm.attachEvent) {
    var r = elm.attachEvent("on" + evType, fn);
    return r;
  } else {
    elm["on" + evType] = fn;
  }
}
```

CSS 兼容：

1. CSS Hack

通过浏览器对特定 CSS 语法的识别差异编写适用于不同浏览器的代码，CSS Hack 针对特定浏览器实现特定样式调整：

IE6 的 CSS 解析器无严格的解析规则，其将 `*` 和 `_` 视为正常的选择器，因此可作为 IE6 专属样式，而 IE7 尽管修复部分解析错误，但仍解析 `*`

- IE6 识别的 Hack：`*`、`_` 和 `*html`
- IE7 识别的 Hack：`*` 和 `*+html`

```css
.box {
    height: 300px;
    *height: 200px;
    _height: 100px;
}
```

```css
.box {
    width: 120px;
}

*html .box {
    width: 80px;
}

*+html .box {
    width: 60px;
}
```

2. 样式兼容性

- `!import`：`width: 80px;` 在不完全支持 `!important` 的 IE6 中生效，用低优先级值覆盖掉 `!important` 声明

```css
.box {
    width: 100px !important; /* IE7+ 及 Firefox */
    width: 80px; /* IE6 */
}
```

- 清除浮动
- 非 IE 中 `padding` 影响 `width` 和 `height`：在盒模型中，不同浏览器对 `width` 和 `height` 的计算方式有所差异，Firefox 等标准浏览器将 `padding` 计算到盒子整体中，而 IE 传统盒模型不会，可通过 `box-sizing: border-box;` 或 `!important` 强制声明，确保宽高计算的一致性

* IE 中 `float` 的双倍 `margin`：通过在浮动元素上添加 `display: inline;` 解决上述问题
* 不同浏览器标签默认 `margin` 和 `padding` 不同

```css
* {
    margin: 0;
    padding: 0;
}
```

###### 基本单位

1. 绝对单位：px + pt(1.33px) + pc(16px) + in(96px) + cm(37.8px) + mm(3.78px)
2. 相对单位：em + rem + vw（视口宽度的百分比） + vh（视口高度的百分比） + %

- px：一个小方块，有特定的位置和颜色
- em：元素的 `font-size`，相对于父元素的字体大小的一个单位。如果父元素没有设置字体大小，那么它会回溯到文档的根元素
- rem：相对于 `html` 元素的 `font-size` 进行相对计算的，浏览器默认 `font-size` 是 16px，则 1rem 等于 16px

设置根元素 `font-size`：

```css
html {
    font-size: 20px;
}
```

百分比设置：

```css
html {
    font-size: 62.5%; /* 16px * 62.5% = 10px */
}
```

媒体查询：

```css
html {
    font-size: 16px;
}

@media (max-width: 1200px) {
    html {
        font-size: 14px;
    }
}

@media (max-width: 768px) {
    html {
        font-size: 12px;
    }
}
```

px 转 rem：

```css
rem = px / root-font-size
```

px 转 vw/vh：

```css
vw = (px / window.innerWidth) * 100
```

###### 基础知识

英寸：描述屏幕的物理大小，注意是屏幕对角线长度
屏幕/图片分辨率：一个屏幕/图片由多少个像素组成
图片分辨率：一个图片由多少个像素组成
Pixel Per Inch：描述屏幕/图片清晰度
Dot Per Inch：点可为屏幕/图片像素点或打印机的墨点
Device Independent Pixels：iOS 尺寸单位为 pt，Android 的尺寸单位为 dp，React Native 中未明确指定单位，但其实均为设备独立像素
Device Pixel Ratio：即设备像素比=物理像素/逻辑像素，通过 `window.devicePixelRatio`（JavaScript）、`min-device-pixel-ratio`（媒体查询）和 `PixelRatio.get`（React Native）获取

Viewport：当前屏幕可见区域，在 Web 中与浏览器窗口相同，不包括浏览器 UI 和菜单栏等
Layout Viewport：页面布局的基准窗口，在 Web 中布局视口即浏览器窗口但不包括 margin、border 和滚动条，在移动端中布局视口默认为 980px，保证 PC 浏览器页面在移动端浏览器上的呈现，用户可手动放大，通过 `document.documentElement.clientWidth` 和 `document.documentElement.clientHeight` 获取

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ce11243ea8b54da68c1b899a235c83d2~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

Visual Viewport：用户在屏幕上真正的可视区域，视觉视口即浏览器窗口，包括滚动条，当用户对浏览器进行缩放时，布局视口大小不变，但视觉视口大小改变，通过 `window.innerWidth` 和 `window.innerHeight` 获取

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bacccff7697542f799ad99cd078de44c~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

Ideal Viewport：页面在移动端展示的理想大小，在浏览器调试移动端时页面给定的像素大小即理想视口大小，单位为设备独立像素，通过 `screen.width` 和 `screen.height` 获取

页面的缩放系数 = 理想视口宽度 / 视觉视口宽度

当页面缩放比例为 100%时，CSS 像素 = 设备独立像素 && 理想视口 = 视觉视口

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3c8562b88a6a4e6fb23c672b64cdcd40~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

Meta Viewport：`meta` 元素即无法由其他 HTML 元相关元素表示的元数据信息

```html
<meta name="viewport" content="width=device-width; initial-scale=1; maximum-scale=1; minimum-scale=1; user-scalable=no;">
```

为让移动端页面获取更好的视觉效果，我们尽可能让布局视口和视觉视口等于理想视口，`device-width` 即理想视口宽度，因此设置 `width=device-width` 即布局视口等于理想视口，而 `initial-scale` = 理想视口宽度 / 视觉视口宽度，因此设置为 1，此时 1 个 CSS 像素等于 1 个设备独立像素，且我们基于理想视口进行布局，因此呈现出来的页面布局在各个设备上大致相似

上面提到的 `width` 并非决定布局视口的唯一因素，设置 `initial-scale` 也影响布局视口，因为布局视口宽度取的是 `width` 和视觉视口宽度的最大值

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/21f5c4626ccf481cb0e026a8608e7284~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

- `window.innerHeight`：获取浏览器视觉视口高度，包括滚动条
- `window.outerHeight`：获取浏览器窗口外部高度，包括侧边栏和调整窗口大小的边框
- `window.screen.Height`：获取浏览器理想视口高度，该值固定为设备分辨率/设备像素比`
- `window.screen.availHeight`：浏览器窗口可用高度
- `document.documentElement.clientHeight`：获取浏览器布局视口高度，包括内边距，不包括外边距、边框和滚动条
- `document.documentElement.offsetHeight`：获取浏览器布局视口高度，包括内边距、边框、外边距和滚动条
- `document.documentElement.scrollHeight`：在不使用滚动条的情况下适合视口中所有内容所需的最小宽度，包括内边距，不包括外边距、边框和滚动条

###### TailwindCSS

TailwindCSS：通过 TailwindCSS 和插件系统自定义全局样式变量，定义一套可复用组件样式，减少冗余代码

1. 间距值：将 `i * 4` 映射为 `i * 0.25rem` 的形式，适用于 padding、margin,、width 和 height 等，若业务需新的间距值，只需调整生成函数即可
2. 高度：通过 `addUtilities` 方法动态适配高度与屏幕尺寸变化，在不同的屏幕断点下分别设置高度和最大高度且通过 `calc(var(--vh, 1vh) * 100)` 动态获取视口高度
3. 透明度：生成 0 到 100 的透明度映射，每个数值对应一个透明度级别，精度达到 1%，为组件的透明度控制提供更高的自由度
4. 安全区域：结合媒体查询通过不同的 `padding` 和 `maxWidth` 设置 `safe-area` 和 `safe-area-extend` 类
5. 字体：根据根元素的 `font-size` 动态调整字体大小
6. 按钮：通过 `addComponents` 方法设置标准化和深色主题按钮 且提供状态样式如 `hover`、`active` 和 `disabled`
7. 边框圆角：`theme('screens')` 获取断点信息即屏幕大小，通过媒体查询动态调整元素的边框圆角半径

###### iOS 输入聚焦时页面自动放大

1. Meta Viewport

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```

- `maximum-scale=1.0`：禁止页面的最大缩放比例大于 1
- `user-scalable=no`：禁用用户缩放功能

2. 设置输入框默认字体大小

```css
input,
textarea {
    font-size: 16px;
}
```

3. 输入框聚焦时 JavaScript 动态调整字体大小，不影响页面其他部分

```js
document.querySelectorAll("input, textarea").forEach((element) => {
  element.addEventListener("focus", () => (element.style.fontSize = "16px"));
  element.addEventListener("blur", () => (element.style.fontSize = ""));
});
```
