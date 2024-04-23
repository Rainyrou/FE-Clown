当 `await` 一个非 Promise 值时，JavaScript 运行时将其包裹在一个 resolved 的 Promise 中

```JavaScript
async function testAwait() {
  const nonPromise = await 123;
  console.log(nonPromise); // 123

  const promise = await Promise.resolve('Hello');
  console.log(promise); // Hello
}

testAwait();
```
