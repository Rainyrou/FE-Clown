```JavaScript
// `generatorToAsync` 为 HOC，接收一个 Generator 函数
function generatorToAsync(generatorFn) {
  return function () {
	 // 执行 `generatorFn` 并返回一个迭代器 `gen`	 
    const gen = generatorFn.apply(this, arguments);
    // 返回一个 Promise
    return new Promise((resolve, reject) => {
		    // `go` 用于递归 `gen`
	        // `key` 为 'next' 或 'throw'，而 `arg` 为传递给生成器的值
      function go(key, arg) {
        let res;
        try {
          res = gen[key](arg);
        } catch (err) {
          return reject(err);
        }
        const { value, done } = res;
	     // `Promise.resolve(value)` 允许在 `yield` 表达式后使用非 Promise 值
        return done
          ? resolve(value)
          : Promise.resolve(value).then(
              (val) => go("next", val),
              (err) => go("throw", err)
            );
      }
      // 首次执行
      go("next");
    });
  };
}

const fn = (num) => new Promise((resolve) => setTimeout(() => resolve(num * 2), 1000));

function* gen() {
  const num1 = yield fn(1);
  console.log(num1);
  const num2 = yield fn(num1);
  console.log(num2);
  const num3 = yield fn(num2);
  console.log(num3);
  return num3;
}

generatorToAsync(gen)().then((res) => console.log(res));
```
