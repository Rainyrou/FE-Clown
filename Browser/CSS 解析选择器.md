浏览器首先加载页面引用的所有 CSS，解析 CSS 并构建 CSSOM，在此过程中，浏览器从右向左解析 CSS 选择器并在 DOM 中查找与选择器匹配的元素，从 DOM 树的最底部开始，向上遍历父节点，直到找到目标元素或到达 DOM 树的根部。若找到目标元素，浏览器将对应的样式应用到该元素上，若一个元素与多个规则匹配，根据 CSS 的层叠和继承规则来决定最终的样式，最后浏览器可能进行重绘和回流

当浏览器尝试将选定的选择器匹配页面中的元素时，它从选择器的最右侧开始，向左移动，原因：

1. 一般来说，页面中的元素比类名、ID 或其他属性多。从右向左解析允许浏览器快速定位特定元素，然后检查该元素是否匹配选择器的其他部分
2. 减少回溯，若从左边的标签或类开始匹配，可能会匹配到多个元素，但随着选择器其他部分的逐步匹配，发现错误匹配后需要回溯重新匹配的可能性更高
