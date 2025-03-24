```js
function createPipeline(...tasks) {
  return function (initialValue) {
    return tasks.reduce(
      (pre, cur) => Promise.resolve(pre).then(cur),
      initialValue
    );
  };
}

const addOne = (x) => x + 1;
const asyncMultiplyTwo = (x) =>
  new Promise((resolve) => setTimeout(() => resolve(x * 2), 500));
const subtractThree = (x) => x - 3;
const pipeline = createPipeline(addOne, asyncMultiplyTwo, subtractThree);
pipeline(5)
  .then((res) => console.log(res)) // 9
  .catch((err) => console.error(err));
```