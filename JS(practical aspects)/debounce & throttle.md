防抖和节流并不能减少事件的触发次数，而是限制事件处理程序的执行次数

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

* 在 JavaScript 中，函数是一等公民，它赋值给变量、作为参数传递给其他函数，或作为函数的返回值。使用箭头函数赋值给 `window.onload`，则当加载事件触发时，调用该函数，确保在函数执行时所有的 DOM 元素均可用，避免尝试访问尚未加载的DOM元素而导致错误，若直接写  `window.onload = yourFunction` 则立即调用 `yourFunction` 并将其返回值而非函数本身赋值给 `window.onload`，除非 `yourFunction` 返回另一函数，否则这样写是不对的
* `debounce` 返回一个新函数，其封装控制计时器的行为，我们需要这个返回的新函数来控制原函数的调用，每次调用返回的新函数时，它决定是否立即调用原始函数
* `timer` 变量存储每次 `setTimeout` 的返回值，以便后续使用 `clearTimeout` 来取消它

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


