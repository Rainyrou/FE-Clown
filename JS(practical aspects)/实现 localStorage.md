实现一个具有过期功能的 `localStorage`，它可以让你控制存储数据的有效期

1. 设置数据时记录过期时间：当数据被存入 `localStorage` 时，一同记录一个过期时间
2. 获取数据时检查过期时间：从 `localStorage` 获取数据时，先检查当前时间是否超过了存储的过期时间
3. 处理过期数据：如果数据已过期，删除它并返回 `null` 或其他默认值
4. 封装逻辑：创建函数来封装设置和获取逻辑

###### 实现

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
```

###### 注意事项

- `localStorage` 仅支持字符串类型。因此，我们需要使用 `JSON.stringify` 和 `JSON.parse` 来处理非字符串类型的数据
- 设置合理的 TTL 以确保数据不会在过早或晚的时间点过期
