```js
const MAX_SIZE = 5 * 1024 * 1024;

const getUsed = () => {
  return Object.entries(localStorage).reduce((total, [key, value]) => {
    return total + (key.length + value.length) * 2;
  }, 0);
};

const setItemWithExpiry = (key, value, ttl) => {
  try {
    const item = { value: value, expiry: new Date().getTime() + ttl };
    const itemStr = JSON.stringify(item);
    const required = (key.length + itemStr.length) * 2,
      remained = MAX_SIZE - getUsed();
    if (required > remained) return false;
    localStorage.setItem(key, itemStr);
    return true;
  } catch (err) {
    if (err.name === "QuotaExceededError") return false;
    throw new Error(err);
  }
};

const getItemWithExpiry = (key) => {
  try {
    const itemStr = localStorage.getItem(key);
    if (!itemStr) return null;
    const item = JSON.parse(itemStr);
    if (item.expiry < new Date().getTime()) {
      localStorage.removeItem(key);
      return null;
    }
    return item.value;
  } catch (err) {
    localStorage.removeItem(key);
    throw new Error(err);
  }
};
```

