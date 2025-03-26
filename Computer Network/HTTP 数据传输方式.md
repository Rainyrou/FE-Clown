1. URL Param：路径参数嵌入 URL 中，如 `/users/:id`，其中 `:id` 即路径参数

```HTTP
GET /users/123
```

2. Query：查询字符串位于 URL 的 `?` 后面，由键值对组成，以 `&` 分隔，用于非主体数据传输，如搜索参数、分页信息等，由于 URL 只允许包含一些特定字符，而其他字符均需经过编码，从而避免解析问题

```HTTP
GET /users?name=Clown&page=250
```

`encodeURIComponent` 用于将这些字符进行编码如：

```JavaScript
const nameVal = encodeURIComponent('雨');
console.log(nameVal); // %E9%9B%A8
```

一些第三方库提供更高级的功能来简化上述过程如 `query-string` 库，用于将对象转换为查询字符串，反之亦然：

```JavaScript
const queryString = require('query-string');
const str = queryString.stringify({
	name: '雨',
	age: '17'
});
console.log(str); // name=%E9%9B%A8&age=17
```

3. Form-urlencoded：一种 MIME 类型 `application/x-www-form-urlencoded` 的数据格式，键值对以 `&` 分隔，而键值间以 `=` 分隔，用于提交表单数据。当使用 `<form>` 标签并选择 POST 提交表单时，若不指定编码方式，默认提交格式为 `application/x-www-form-urlencoded`，其与 URL 的查询字符串相似，但查询字符串是位于 URL 后的，而 `application/x-www-form-urlencoded` 的数据在 HTTP 请求的 body 中
4. Form-data：一种 MIME 类型 `multipart/form-data` 的数据格式，用于传输二进制（无需进行 URL 编码）或文件数据，当使用 HTML 表单上传文件时，若不指定编码方式，默认提交格式为 `multipart/form-data`，Form-data 不以 `&` 分隔数据，而以特定字符串如 `----------一串数字` 作为 boundary 分隔符
5. JSON：JavaScript Object Notation 是一种易于人读写和机器解析的数据格式，其数据格式为纯文本，使用 JSON 编码，无需进行 URL 编码，当发送 JSON 数据时，HTTP 请求的 Content-Type 头应设置为 `application/json`

```JSON
{
	name: 'clown',
	age: 21
}
```
