```js
const mergePromise = (promisesFn) => {
  let sequence = Promise.resolve();
  const ans = [];
  promisesFn.forEach(
    (promiseFn) =>
      (sequence = sequence.then(() => promiseFn()).then((res) => ans.push(res)))
  );
  return sequence.then(() => ans);
};

const timeout = (time) =>
  new Promise((resolve) => setTimeout(() => resolve(), time));
const promise1 = () => timeout(2000).then(() => "promise1");
const promise2 = () => timeout(1000).then(() => "promise2");
const promise3 = () => timeout(3000).then(() => "promise3");
mergePromise([promise1, promise2, promise3]).then((res) => console.log(res));
```