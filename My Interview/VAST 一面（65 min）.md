1. 事件循环及输出题

```js
console.log("script start");

async function async1() {
  await async2();
  console.log("async1 end");
}

async function async2() {
  console.log("async2 end");
}

async1();

setTimeout(function () {
  console.log("setTimeout");
}, 0);

new Promise((resolve) => {
  console.log("Promise");
  resolve();
})
  .then(function () {
    console.log("promise1");
  })
  .then(function () {
    console.log("promise2");
  });

console.log("script end");
```

2. 手撕：自定义 React Hook 实现按钮锁即点击后 5s 内无法点击

3. HTTP 状态码

4. 实习接触的跨域

5. WebSocket

6. 浏览器缓存

7. SSR

8. TypeScript

9. TailwindCSS

10. 手撕：判断列表类型
