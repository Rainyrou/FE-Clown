Inline Frame 为 HTML 元素，其通过创建独立 DOM 和 JavaScript 环境，允许在某页面中显示其他页面内容，与原页面保持交互独立，若 iframe 与外部页面同源，则两者直接通过 JavaScript 访问彼此的全局变量、函数和 DOM 等，若 iframe 与外部页面跨域，通过 `postMessage` 和 `onmessage` 跨域通信

```html
<iframe src="https://example.com" width="600" height="400" title="example" sandbox="allow-scripts allow-same-origin"
  loading="lazy"></iframe>
```

1. 通过  `sandbox`  属性限制 Iframe 行为

- `allow-same-origin`：同源策略
- `allow-scripts`：执行脚本
- `allow-forms`：提交表单
- `allow-popups`：打开新窗口

2. 模块化开发
3. 各个 Iframe 均创建其独立渲染进程，大量 Iframe 导致页面卡顿，不利于 SEO
