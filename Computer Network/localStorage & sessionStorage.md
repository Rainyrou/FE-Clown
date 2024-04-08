localStorage 和 sessionStorage 是 HTML5 标准，其基于同源策略，在浏览器上以字符串形式存储，localStorage 本地存储数据持久化，除非主动删除，sessionStorage 在一个会话期存储数据，它们均为只读属性，不能赋值，但可访问和修改其内容

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
