```js
const serialFetch = async (urls) => {
  const ans = [];
  for (const url of urls) {
    const response = await fetch(url);
    if (!response.ok) throw new Error(response.status);
    const data = await response.json();
    ans.push(data);
  }
  return ans;
};

const urls = [
  "https://jsonplaceholder.typicode.com/posts/1",
  "https://jsonplaceholder.typicode.com/posts/2",
  "https://jsonplaceholder.typicode.com/invalid-url",
  "https://jsonplaceholder.typicode.com/posts/3",
];

serialFetch(urls)
  .then((res) => console.log(res))
  .catch((err) => console.error(err));
```

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



