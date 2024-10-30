localStorage 和 sessionStorage 为 HTML5 标准，其基于同源策略，在浏览器上以字符串形式存储，localStorage 本地存储数据持久化，除非主动删除，其在同一域名下的所有标签页和窗口间是共享的，sessionStorage 在一个会话期存储数据，其与特定的标签页或窗口相关联，在不同标签页或窗口中打开同一页面时，数据不会共享。它们均为只读属性，不能赋值，但可访问和修改其内容，其存储上限为每个域名 origin 5MB，存储上限是出于性能考虑，以下操作是同步的，数据读写删除阻塞主线程，若存储数据量过大影响页面性能

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
