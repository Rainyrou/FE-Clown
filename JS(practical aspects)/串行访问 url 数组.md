```js
const serialFetch = async (urls) => {
  const ans = [];
  for (const url of urls) {
    const response = await fetch(url);
    if (!response.ok) throw new Error(`${url}`, `${response.status}`);
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