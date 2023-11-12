```JavaScript
function getURLParams(url) {
    let params = {};
    url.replace(/[?&]+([^=&]+)=([^&]*)/gi, function(match, key, value) {
        key = decodeURIComponent(key);
        value = decodeURIComponent(value);
        if(params[key]) {
            if(Array.isArray(params[key])) {
                params[key].push(value);
            } else {
                params[key] = [params[key], value];
            }
        } else {
            params[key] = value;
        }
    });
    return params;
};

let url = "http://example.com/?param1=abc&param2=def";
console.log(getURLParams(url));
```

正确的输出结果：

```
{ param1: 'abc', param2: 'def' }
```

```
/[?&]+([^=&]+)=([^&]*)/gi
```

两个括号对 `()` 是捕获组，它们分别捕获 URL 参数的键和值

- `([^=&]+)`：匹配不是 `=` 和 `&` 的一个或多个字符，表示 URL 参数的键
- `([^&]*)`：匹配除了 `&` 外的零个或多个字符，表示 URL 参数的值

当 `replace` 方法与一个包含捕获组的正则表达式一起使用时，它的回调函数将接收每个匹配的完整内容作为第一个参数，后面是每个捕获组的内容。因此在这段代码中，回调函数的 `match` 参数是整个匹配，而 `key` 和 `value` 参数分别是两个捕获组的内容

正则表达式的工作原理是通过状态机和模式匹配来查找字符串中的匹配项。当我们使用 `String.replace` 方法与正则表达式一起时，JavaScript 引擎会遍历字符串，使用正则表达式查找匹配项，并为每个匹配调用回调函数。如果正则表达式包含捕获组，这些捕获组的内容会作为额外的参数传递给回调函数

`String.replace` 返回一个字符串，通常，这个返回的字符串会用来替换原始匹配。但如果我们只是返回原始匹配的内容，那么源字符串就不会发生任何变化。这种行为允许我们使用 `replace` 方法来遍历和处理正则表达式的所有匹配，而不实际替换任何内容
