```JavaScript
const setItemWithExpiry = (key, value, ttl) => {
  const item = {
    value: value,
    expiry: Date.now().getTime() + ttl,
  };
  localStorage.setItem(key, JSON.stringify(item));
};

const getItemWithExpiry = (key) => {
  const itemStr = localStorage.getItem(key);
  if (!itemStr) return null;
  const item = JSON.parse(itemStr);
  if (item.expiry < Date.now().getItem()) {
    localStorage.removeItem(key);
    return null;
  }
  return item.value;
};

setItemWithExpiry("myKey", "myValue", 3600000);
console.log(getItemWithExpiry("myKey")); // myValue
```

![[1699858192700.png]]
