localStorage 和 sessionStorage 为 HTML5 标准，其基于同源策略，均为只读属性，无法赋值但可通过 API 访问和修改其内容，在浏览器上以字符串形式存储，存储上限为每个域名 origin 5MB。localStorage 本地存储数据持久化，除非主动删除，在同一域名下的所有标签页和窗口中数据是共享的，sessionStorage 在一个会话期间存储数据，其与特定的标签页或窗口相关联，在不同的标签页或窗口中数据无法共享。数据读写删除均为同步操作，因此会阻塞浏览器主线程，若存储的数据量过大则影响页面性能，当超出存储上限时，浏览器抛出  `QuotaExceededError`  异常，检测溢出的方法为计算当前存储的数据量和即将存储的数据量，若超出存储上限，则通过 LRU 算法移除旧数据，同时在写入时以 try-catch 来捕获 `QuotaExceededError`  异常

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

用户手动删除 localStorage 缓存：

- 不可靠存储：localStorage 底层定位为浏览器非核心数据的临时缓存，其存储于浏览器沙盒，用户通过 `localStorage.clear`、浏览器设置 → 隐私清理和第三方工具等主动删除的行为并非意外行为，浏览器自身在隐私模式关闭和存储空间不足时主动删除 localStorage 缓存，它并没有"数据不可丢失"的底层保障，核心数据本就不该存于 localStorage
- 数据预校验与兜底：在项目初始化或浏览器渲染完成时校验数据，若数据丢失/非法，恢复默认值而非抛出错误；前端存储为拷贝，服务端为唯一数据源，任何前端缓存丢失，均通过同步服务端数据恢复，此外前端项目不应以前端缓存和服务端数据为前提，还需有兜底措施以防止白屏

非 Chrome 兼容性提示且一天只显示一次：

1. 判断浏览器类型
2. 通过 localStorage 和 Cookie 存储最后一次提示对应时间戳，每次访问判断当前时间戳与存储时间戳的差值是否大于 24 小时，若大于则重置提示状态，当某标签页存储提示时间戳后，其他标签页通过监听 `storage` 事件自动更新状态，确保同一浏览器的多个标签页共享提示状态，避免重复提示
