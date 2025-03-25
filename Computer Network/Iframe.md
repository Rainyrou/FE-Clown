Inline Frame 为 HTML 元素，其通过创建一个独立的 DOM 和 JavaScript 环境，允许在一个页面中显示其他页面内容，同时与原页面保持交互独立性，若 iframe 与外部页面同源，则两者可直接通过 JavaScript 访问彼此的全局变量、函数和 DOM 等，否则即 iframe 与外部页面跨域，通过 `postMessage` 和 `onmessage` 进行跨域通信

```html
<iframe src="https://example.com" width="600" height="400" title="example" sandbox="allow-scripts allow-same-origin"
  loading="lazy"></iframe>
```

1. 通过  `sandbox`  属性限制 Iframe 的行为

- `allow-same-origin`：保持同源策略
- `allow-scripts`：允许执行脚本
- `allow-forms`：允许提交表单
- `allow-popups`：允许打开新窗口

2. 模块化开发，独立加载子模块，适合微前端架构
3. 在 Chromium 中各个 Iframe 均创建自己独立的渲染进程，大量 Iframe 导致内存占用高，页面卡顿，同时搜索引擎可能忽略 Iframe 内容，对 SEO 不友好，响应式设计需动态调整宽高
4. XSS 攻击即嵌入恶意脚本的 Iframe，点击劫持即透明 Iframe 覆盖按钮，诱导用户点击，Phishing 钓鱼攻击即伪造登录页面的 Iframe
