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
  if (!itemStr) return null;
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

在浏览器控制台输入上述代码，即可打印预期输出信息：

![[1699858192700.png]]
