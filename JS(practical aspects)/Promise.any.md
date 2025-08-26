```js
Promise._any = function (promises) {
  return new Promise((resolve, reject) => {
	promises = [...promises];
    if (promises.length === 0) 
	    return reject(new AggregateError([], "No promises provided"));
    const errors = [];
    let count = 0,
      hasFulfilled = false;
    promises.forEach((promise, index) =>
      Promise.resolve(promise)
        .then((res) => {
          if (!hasFulfilled) {
            hasFulfilled = true;
            return resolve(res);
          }
        })
        .catch((err) => {
          errors[index] = err;
          if (++count === promises.length && !hasFulfilled)
            return reject(new AggregateError(errors, "All promises were rejected"));
        })
    );
  });
};

Promise._any([Promise.reject(1), Promise.resolve(2), Promise.reject(3)]).then(
  console.log
); // 2

Promise._any([Promise.reject("Error1"), Promise.reject("Error2")]).catch(
  (e) => {
    console.log(e.name); // AggregateError
    console.log(e.errors); // ['Error1', 'Error2']
    console.log(e.message); // All promises were rejected
  }
);
```