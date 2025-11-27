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

