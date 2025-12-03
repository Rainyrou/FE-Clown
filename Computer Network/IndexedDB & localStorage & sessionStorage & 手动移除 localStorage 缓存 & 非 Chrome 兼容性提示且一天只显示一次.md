IndexedDB：

* 核心定位：浏览器原生非关系型持久化存储系统，遵循同源策略，异步操作避免阻塞浏览器主线程，GB 级结构化数据存储，默认永久存储，可强制持久化避免被浏览器移除，存储模型为数据库（同源隔离）→ 对象仓库（类关系型数据库表）→ 数据项（键值对形式，主键唯一标识）
* 事务机制：增删改查操作包裹于事务中执行，遵循 ACID 特性，事务有只读（并发执行）、读写（串行加写锁）、版本变更（修改数据库结构）
* 索引机制：根据数据属性创建唯一和非唯一索引，底层基于 B 树索引设计，查询时间复杂度为 O (logN)，支持范围查询和游标遍历

localStorage & sessionStorage：

localStorage 和 sessionStorage 为 HTML5 标准，其基于同源策略，只读属性，通过 API 访问和修改其内容，数据读写删除为同步操作，以字符串形式存储，localStorage 本地持久存储数据，除非主动删除，在同一域名下的所有标签页和窗口中数据共享，sessionStorage 在单个会话期间存储数据，与特定标签页相关联，在不同标签页中数据无法共享，存储上限为各个域名 5MB，当超出存储上限时，浏览器抛出  `QuotaExceededError` 异常，判断溢出的方式为计算当前存储和即将存储的数据量，若超出存储上限则通过 LRU 算法移除旧数据，在写入时以 try-catch 捕获 `QuotaExceededError`  异常

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

通过 `localStorage.clear` 或在浏览器存储空间不足时主动移除

非 Chrome 兼容性提示且一天显示一次：判断浏览器类型，通过 localStorage 和 Cookie 存储最后提示对应时间，每次访问时判断当前时间与存储时间差值是否大于 24 小时，若大于则重置提示状态，当某标签页存储提示时间戳后，其他标签页通过监听 `storage` 事件自动更新状态
