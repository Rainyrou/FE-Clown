防抖和节流并不能减少事件的触发次数，而是限制事件处理程序的执行次数
防抖用于避免事件处理程序在短时间内频繁触发如输入框实时搜索和浏览器窗口 `resize` 事件，每次调用事件处理程序时，防抖函数重置定时器，在设定的时间间隔内，若无新的触发，则执行最后一次触发，否则重新计时
节流用于限制高频率事件如页面滚动和点击按钮，在设定的时间间隔内，无论触发多少次，事件处理程序只执行一次，一旦函数执行则开始计时，在设定的时间间隔内忽略新的触发
`debounce` 返回一个新函数，其封装控制计时器的行为，我们需要这个返回的新函数来控制原函数的调用，每次调用返回的新函数时，它决定是否立即调用原始函数
`timer` 变量存储每次 `setTimeout` 的返回值，以便后续使用 `clearTimeout` 来取消它

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

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>debounce</title>
</head>
<body>
    <button id="debounce">click</button>
    <script>
        window.onload = () => {
            const myDebounce = document.getElementById('debounce');
            myDebounce.addEventListener('click', debounce(sayDebounce));
        }

        function debounce(fn) {
            let timer = null;
            return function (...args) {
                clearTimeout(timer);
                timer = setTimeout(() => fn.call(this, args), 1000);
            }
        }
  
        const sayDebounce = () => console.log('debounce success');
    </script>
</body>
</html>
```

在 JavaScript 中函数为一等公民，它赋值给变量、作为参数传递给其他函数或作为函数的返回值。使用箭头函数赋值给 `window.onload`，则当加载事件触发时，调用该函数，确保在函数执行时所有的 DOM 元素均可用，避免尝试访问尚未加载的DOM元素而导致错误，若直接写  `window.onload = yourFunction` 则立即调用 `yourFunction` 并将其返回值而非函数本身赋值给 `window.onload`，除非 `yourFunction` 返回另一函数，否则这样写是不对的

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>throttle</title>
</head>
<body>
    <button id="throttle">click</button>
    <script>
        window.onload = () => {
            const myThrottle = document.getElementById('throttle');
            myThrottle.addEventListener('click', throttle(sayThrottle, 1000));
        }

        function throttle(fn, delay) {
            let startTimer = Date.now();
            return function(...args) {
                let curTimer = Date.now();
                if(curTimer - startTimer > delay) {
                    fn.call(this, args);
                    startTimer = Date.now();
                }
            }
        }

        const sayThrottle = () => console.log('throttle success');
    </script>
</body>
</html>
```

