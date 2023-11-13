要在 `localStorage` 中存储带有时效性的数据，你需要将数据和 expiry time 一块存储。然后，在读取数据时首先检查这个过期时间，如果当前时间已超过过期时间，说明数据已过期，需要进行相应处理，如删除过期数据，重新获取等。这种方法的优势在于它简单且直接

```JavaScript
function setItemWithExpiry(key, value, ttl) {
  const now = new Date();
  const item = {
    value: value,
    expiry: now.getTime() + ttl,
  };
  localStorage.setItem(key, JSON.stringify(item));
}

function getItemWithExpiry(key) {
  const itemStr = localStorage.getItem(key);
  if (!itemStr) {
    return null;
  }
  const item = JSON.parse(itemStr);
  const now = new Date();
  if (now.getTime() > item.expiry) {
    localStorage.removeItem(key);
    return null;
  }
  return item.value;
}

setItemWithExpiry("myKey", "myValue", 3600000);

const value = getItemWithExpiry("myKey");
console.log(value); // myValue
```

在浏览器上控制台输入上述代码，即可打印预期的输出信息：

![[1699858192700.png]]

###### 注意事项：

- `localStorage` 的存储空间有限（一般为 5MB），注意数据大小。对于更复杂的数据存储需求，考虑使用 `IndexedDB`
- 在设计数据结构时，应确保一致性，以便正确解析数据
- `localStorage` 是 Web 浏览器提供的 API，用于在用户浏览器中存储数据，但它不是 Node.js 的一部分。因此，当在 Node.js 环境中运行此代码时，会抛出 `localStorage is not defined` 的错误
