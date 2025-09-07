页面流畅性由浏览器在屏幕上绘制的帧率 FPS 决定，当每秒绘制的帧数达到 60 时，页面为流畅的，小于该值时，用户感觉明显卡顿

```
1s / 60帧 = 16.67ms/帧
```

![[Pasted image 20241116091325.png]]

1. 处理用户交互事件如鼠标点击、键盘输入和触摸等
2. 浏览器解析并执行页面中的 JavaScript 如处理事件监听器的代码、`setTimeout` 和 `setInterval` 注册的任务、Promise 微任务队列等
3. 帧开始阶段处理窗口尺寸变化、页面滚动和 CSS 动画等，判断页面是否需要重绘回流
4. 执行通过 `requestAnimationFrame` 注册的任务，用于实现高性能动画，修改 DOM 或样式
5. 若 DOM 或 CSS 样式变化，浏览器重新计算元素的几何属性和页面布局即重绘
6. 绘制所有可见元素，生成页面的像素数据
7. 若前面的工作耗时少于 16ms，浏览器将剩余时间分配给 `requestIdleCallback`，`requestIdleCallback` 注册的任务用于执行低优先级操作如预加载图片和日志上报等

在浏览器没有 `requestAnimationFrame` 前，动画通过 `setTimeout` 或 `setInterval` 实现，但此种方式存在明显缺陷：

1. 回调执行时机不确定：`setTimeout` 和 `setInterval` 属于宏任务，其执行必须等到主线程上所有同步任务及所有优先级更高的任务执行完毕，因此定时器的回调函数无法保证在指定时间精确触发
2. 时间间隔不适配屏幕刷新率：浏览器在屏幕上绘制的帧率通常为 60Hz，对应约 16.6ms 刷新一次，而定时器的时间间隔由开发人员手动设置，浏览器并不保证回调函数与屏幕刷新同步，若时间间隔太短，浏览器尝试绘制多余帧，过度渲染，若时间间隔太长，动画帧率低于屏幕刷新率，延迟渲染，页面卡顿

`requestAnimationFrame` 的优势：

1. 自动与屏幕刷新同步：`requestAnimationFrame` 用于动画，其根据屏幕刷新率自动调度回调函数，每次调用 `requestAnimationFrame` 后，浏览器在下一帧开始前执行回调函数并将其内容渲染到屏幕上，最大化利用帧时间
2. 效率更高：当页面于后台运行时，`requestAnimationFrame` 暂停回调触发，减少性能开销

`requestAnimationFrame` 的回调不是自动循环的，需在每次回调中手动调用，若每帧执行的任务过多，使用 `requestAnimationFrame` 仍可能超过 16.6ms，导致帧率下降

```js
let offsetTop = 0;
const div = document.querySelector(".div");

function animate() {
  div.style.transform = `translateY(${(offsetTop += 10)}px)`;
  window.requestAnimationFrame(animate);
}

animate();
```

如此一来，若浏览器一直处于忙碌状态，`requestIdleCallback` 注册的任务永不执行，此时通过设置 `timeout` 来保证执行

```js
var handle = window.requestIdleCallback(callback[, options])
```

- `callback`：回调即空闲时需执行任务，其接收一个 `IdleDeadline` 对象，而 `IdleDeadline` 包含 `didTimeout` -> 表示任务是否超时，`timeRemaining` 表示当前帧剩余的时间
- `options`：接收 `timeout`，`timeout` 表示超过指定的时间后若任务仍未执行则强制执行

`cancelIdleCallback`  返回一个唯一 ID，用于取消任务

不建议在 `requestIdleCallback` 中操作 DOM 的原因：

1. `requestIdleCallback` 的执行发生于一帧的最后，也就是说此时页面已完成布局和绘制
2. 在 `requestIdleCallback` 中修改 DOM 导致浏览器在下一帧重新计算布局 Reflow 和绘制 Repaint，此操作不仅耗时，还可能破坏当前帧的流畅性，增加卡顿风险。DOM 操作建议在 `requestAnimationFrame` 中进行，因为 rAF 为浏览器绘制流程的一部分，此时修改 DOM 在下一帧被合并处理而不会打断当前的渲染流程

不建议在 `requestIdleCallback` 中使用 Promise 的原因：

1. 在事件循环中，Promise 回调为微任务，优先级高于 `requestIdleCallback`，当 `requestIdleCallback` 回调中创建 Promise 后，Promise 回调在 `requestIdleCallback` 完成后立即执行，即使帧时间已用尽，Promise 仍在当帧执行，可能让当前帧超过 16ms 导致掉帧
2. `requestIdleCallback` 设计初衷为处理低优先级任务，而 Promise 回调的优先级较高，不适用于此场景
