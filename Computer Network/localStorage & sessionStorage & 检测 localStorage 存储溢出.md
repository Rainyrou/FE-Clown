localStorage 和 sessionStorage 为 HTML5 标准，其基于同源策略，在浏览器上以字符串形式存储，localStorage 本地存储数据持久化，除非主动删除，其在同一域名下的所有标签页和窗口间是共享的，sessionStorage 在一个会话期存储数据，其与特定的标签页或窗口相关联，在不同标签页或窗口中打开同一页面时，数据不会共享。它们均为只读属性，不能赋值，但可访问和修改其内容，其存储上限为每个域名 origin 5MB，存储数据以  UTF-16 字符串格式保存，每个字符占 2 字节，以下操作是同步的，数据读写删除阻塞主线程，若存储数据量过大影响页面性能，当超出存储上限时，浏览器抛出  `QuotaExceededError`  异常，检测溢出的通常做法为尝试写入数据并捕获 `QuotaExceededError`  异常，但这种做法只有在写入时才能检测溢出而无法提前预判，因此先计算当前存储的数据量，加上即将存储的数据量，若超出存储上限，则通过 LRU 策略清理数据，同时在写入时仍以 try-catch 来捕获 `QuotaExceededError`  异常

```js
const getSpace = () =>
  Object.entries(localStorage).reduce(
    (total, [key, value]) => total + (key.length + value.length) * 2,
    0
  );

const setLocalStorage = (key, value) => {
  const needSpace = (key.length + value.length) * 2,
    remainSpace = 5 * 1024 * 1024 - getSpace();
  if (needSpace > remainSpace) {
    console.error("所需空间超出上限");
    return false;
  }
  try {
    localStorage.setItem(key, value);
    return true;
  } catch (err) {
    if (err.name === "QuotaExceededError") {
      console.error("实际写入时发生溢出");
      return false;
    }
    throw new Error(err);
  }
};
```

储存数据：

```JavaScript
localStorage.setItem('key', 'value');
sessionStorage.setItem('key', 'value');
```

获取数据

```JavaScript
localStorage.getItem('key');
sessionStorage.getItem('key');
```

删除数据

```JavaScript
localStorage.removeItem('key');
sessionStorage.removeItem('key');
```

清空数据

```JavaScript
localStorage.clear();
sessionStorage.clear();
```
