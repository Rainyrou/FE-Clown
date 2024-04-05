`JSON.parse` 不能解析的数据：

* undefined
* Function
* Date,RegExp, Map, Set,Error
* 稀疏数组，缺失项解析为 `null`
* 循环引用，原型链上的属性

```JavaScript
let json = '{"name":"Clown", "age":21}';
let obj = eval("(" + json + ")");
```

