防抖 `debounce` 与节流 `throttle` 都是控制事件处理函数执行频率的方法。在面对高频触发的事件如 `onscroll` 时，如果其事件处理函数涉及资源密集型操作如 DOM 操作或服务端请求，不加控制地执行会导致性能问题。防抖和节流并不能减少事件触发次数，而是通过有策略地限制事件处理函数的执行次数，从而提高性能

### 防抖

#### 非立即防抖

当事件持续触发时，事件处理函数是完全不执行的，而是等最后一次触发结束后经过一段时间再去执行

实现思路：每次触发事件时都取消之前的延时调用方法并重设定时器

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
            return function(...args) {
                clearTimeout(timer);
                timer = setTimeout(() => {
                    fn.call(this, args);
                }, 1000);
            }
        }

        const sayDebounce = () => console.log('debounce success!');
    </script>
</body>
</html>
```

![[1696900254081.png]]

##### 底层原理

###### window.onload

在浏览器中，存在一种机制可以让你指定当某个事件（如页面加载、按钮点击、鼠标移动等）发生时应执行的函数。这些函数被称为事件处理程序或事件侦听器

在 JavaScript 中，函数是一等公民。这意味着函数可以赋值给变量、作为参数传递给其他函数，或作为函数的返回值。当您使用 `window.onload = () => { ... }`，实际上是将一个函数（在这种情况下是一个箭头函数）赋值给 `window.onload`。这意味着当"加载"事件发生时，该函数将被调用

如果直接写 `window.onload = yourFunction();`（注意括号），则会立即调用 `yourFunction` 并将其返回值（而不是函数本身）赋值给 `window.onload`。除非 `yourFunction` 返回另一个函数，否则这种方法是错误的

当你给 `window.onload` 赋值一个函数时，实际上是告诉浏览器："当页面加载完成时，请执行该函数。"这确保了在函数执行时，所有的 DOM 元素都已可用，避免了尝试访问尚未加载的元素而可能导致的错误

使用 `window.onload` 确保当你尝试访问或操作页面上的 DOM 元素时，这些元素已被完全加载和初始化。在浏览器解析和呈现一个 HTML 页面时，它会从顶部到底部逐行读取和执行代码。如果你试图在 DOM 元素被完全加载之前访问它，你会得到一个 `null` 或 `undefined`，因为该元素在此时尚不存在

考虑以下场景：假设你的 JavaScript 代码位于 HTML 文档的头部或文档的任何部分，而在这些代码之前，目标元素尚未被定义和加载。此时尝试访问该元素会导致错误

###### 为什么 debounce 函数要返回一个函数？

`debounce` 函数的目标是确保一个函数不被频繁、快速地连续调用，而是确保在一定的延迟时间过后只执行一次

为了实现上述效果，我们必须返回一个新函数，这个新函数封装了这种计时器控制的行为

我们需要一个中间人（返回的新函数）来控制原始函数的调用。每次返回的新函数被调用时，它会决定是否立即调用原始函数、稍后调用它，还是完全忽略调用。如果我们不使用这个中间人，我们就无法控制原始函数何时被调用，这样 `debounce` 就失去了其目的

如果 `debounce` 函数被调用时直接清除计时器，那么每次我们试图创建一个防抖函数时，之前的防抖函数都会失效。这并不是我们想要的效果。我们希望每个返回的新函数都能保留其自己的计时器状态

当返回的新函数被连续调用时，我们希望取消之前的调用，并重新开始计时。这样，只有在连续的调用停止并超过指定的延迟时间后，原始函数才会被调用。这确保了在高频事件（例如，用户连续快速地输入文本）中，原始函数只在事件结束后被调用一次

###### 为什么把 setTimeout 赋给 timer 呢？

`timer` 变量存储每次 `setTimeout` 的返回值，以便后续使用 `clearTimeout` 来取消它

`setTimeout` 函数的返回值是一个整数，它代表定时器的唯一标识符。这个唯一标识符用于之后取消定时器的操作

```JavaScript
const timerID = setTimeout(() => {
    console.log("This runs after 1 second");
}, 1000);

// 如果在 1 秒之前想取消这个定时器
clearTimeout(timerID);
```

###### this

`fn.call(this, args)` 的 `this` 指的是当前正在执行的匿名函数的上下文。在 JavaScript 中，一个函数的 `this` 值是在其被调用时决定的，不是在其被声明时。在这个情况下，匿名函数是作为事件监听器被调用的，所以它的 `this` 值是触发事件的元素，即按钮元素（`<button id="debounce">click</button>`）

因此，当你在 `setTimeout` 的回调中使用 `fn.call(this, args)` 时，你实际上是将 `sayDebounce` 函数的上下文设置为了按钮元素

#### 节流

当事件持续触发时，节流可以稀释事件处理函数的执行频率

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

        const sayThrottle = () => console.log('throttle success!');
    </script>
</body>
</html>
```

![[1696905863882.png]]
