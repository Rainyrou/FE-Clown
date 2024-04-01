当你使用 `await` 关键字等待一个非 Promise 值时，JavaScript 运行时将该值包裹在一个 resolved 的 Promise 中

```JavaScript
async function testAwait() {
  const nonPromise = await 123;
  console.log(nonPromise); // 123

  const promise = await Promise.resolve('Hello');
  console.log(promise); // Hello
}

testAwait();
```

###### 底层原理

当使用 `await` 关键字时，JavaScript 引擎会检查其后面的表达式的值：

- 如果值不是一个 Promise，引擎使用 `Promise.resolve()` 将其转换为一个 resolved 的 Promise
- 如果值是一个 Promise，引擎直接等待它

这种行为确保 `await` 关键字的一致性和灵活性，允许它与 Promise 和非 Promise 值一样有效地工作
