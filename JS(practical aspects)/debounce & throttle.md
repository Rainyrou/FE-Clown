* 防抖和节流无法减少事件的触发次数，而是减少事件处理程序的执行次数
* 防抖用于避免事件处理程序在短时间内频繁触发如输入框实时搜索和浏览器窗口 `resize` 事件，每次调用事件处理程序时，防抖函数重置定时器，在设定的时间间隔内，若无新的触发，则执行最后一次触发，否则重新计时
* 节流用于限制高频率事件如页面滚动和点击按钮，在设定的时间间隔内，无论触发多少次，事件处理程序只执行一次，一旦函数执行则开始计时，在设定的时间间隔内忽略新的触发
* 节流 150ms 间隔、300ms 内连续触发的执行次数：Lodash 的默认配置（首尾触发），3 次（0ms, 150ms, 300ms）；时间戳（首触发），2次（0ms, 150ms）；定时器（尾触发），2次（150ms, 300ms）
* `debounce` 返回新函数，其封装控制计时器的行为，通过返回的新函数控制原函数调用
* `timer` 变量存储每次 `setTimeout` 的返回值，以便后续通过 `clearTimeout` 取消

```js
// 支持立即触发
function debounce(fn, wait, immediate = false) {
  let timer = null;
  return function (...args) {
    const context = this;
    if (!timer && immediate) fn.apply(context, args);
    clearTimeout(timer);
    timer = setTimeout(() => {
      if (!immediate) fn.apply(context, args);
      timer = null;
    }, wait);
  };
}

// 支持一次触发
function debounce(fn, wait) {
  let timer = null,
    hasCalled = false;
  return function (...args) {
    const context = this;
    if (!hasCalled) {
      fn.apply(context, args);
      hasCalled = true;
    }
    clearTimeout(timer);
    timer = setTimeout(() => (hasCalled = false), wait);
  };
}

function throttle(fn, delay, immediate = false) {
  let timer = null,
    lastCall = 0;
  return function (...args) {
    const context = this,
      now = Date.now();
    if (immediate && now - lastCall > delay) {
      fn.apply(context, args);
      lastCall = now;
    } else if (!timer) {
      timer = setTimeout(() => {
        fn.apply(context, args);
        lastCall = Date.now();
        timer = null;
      }, delay);
    }
  };
}
```
