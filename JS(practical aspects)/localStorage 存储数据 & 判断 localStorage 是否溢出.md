localStorage 存储数据：

```JavaScript
const setItemWithExpiry = (key, value, ttl) => {
  const item = { value: value, expiry: new Date().getTime() + ttl };
  localStorage.setItem(key, JSON.stringify(item));
};

const getItemWithExpiry = (key) => {
  const itemStr = localStorage.getItem(key);
  if (!itemStr) return null;
  const item = JSON.parse(itemStr);
  if (item.expiry < new Date().getTime()) {
    localStorage.removeItem(key);
    return null;
  }
  return item.value;
};

setItemWithExpiry("myKey", "myValue", 3600000);
console.log(getItemWithExpiry("myKey")); // myValue
```

判断 localStorage 是否溢出：

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