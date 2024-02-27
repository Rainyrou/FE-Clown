1. URL Param：

```HTTP
GET /users/123
```

路径参数通常嵌入 URL 中，如 `/users/:id`，其中 `:id` 就是一个路径参数

2. Query：

```HTTP
GET /users?name=Clown&page=250
```

查询字符串位于 URL 的 `?` 后面，由键值对组成，并使用 `&` 分隔，用于非主体数据传输，如搜索参数、分页信息等

由于 URL 只允许包含一些特定字符，而其他的（如非英文字符、特殊字符等）都需要经过编码。如此一来，这些字符便可安全地包含在 URL 中，而不会导致解析问题

JavaScript 提供了一个名为 `encodeURIComponent` 的函数，用于将这些字符进行编码，如：

```JavaScript
const nameVal = encodeURIComponent('雨');
console.log(nameVal); // %E9%9B%A8
```

虽可直接使用 `encodeURIComponent` 进行编码，但在实际开发中，我们通常需要处理多个参数，一些第三方库提供了更高级的功能来简化这一过程，如 `query-string` 库，它允许将对象转换为查询字符串，反之亦然，如：

```JavaScript
const queryString = require('query-string');
const str = queryString.stringify({
	name: '雨',
	age: '17'
});
console.log(str); // name=%E9%9B%A8&age=17
```

3. Form-urlencoded：

这是一种 MIME 类型 (`application/x-www-form-urlencoded`) 的数据格式，数据键值对之间用 `&` 分隔，而键与值之间用 `=` 分隔，其用于提交表单数据

当我们在网页上使用 `<form>` 标签并选择 POST 方式提交表单时，若不指定其他编码方式，默认的提交格式是 `application/x-www-form-urlencoded`。如有一个有 `name` 和 `age` 字段的表单，提交后的数据可能是`name=Clown&age=21`

这种格式和 URL 的查询字符串相似，但查询字符串是附加在 URL 后的，而 `application/x-www-form-urlencoded` 格式的数据是在 HTTP 请求的 body 中

使用 `application/x-www-form-urlencoded`上传文件或提交大量数据可能会很慢，因为所有数据都需要编码，`multipart/form-data` 格式更为合适

4. Form-data:

- 这是一种 MIME 类型 (`multipart/form-data`) 的数据格式，用于传输二进制或文件数据
- 当使用 HTML 表单上传文件时，默认的 Content-Type 是 `multipart/form-data`
- Form-data 不使用 `&` 分隔数据，而使用特定字符串（如 `----------一串数字`）作为 boundary 分隔符
- 因为它支持二进制数据，所以无需进行 URL 编码

底层格式：

- 每部分数据都由 boundary 分隔符隔开
- 文件或二进制数据均可直接作为数据的一部分
- 除了文件或数据，每部分还包含了描述该数据的头信息（如 Content-Disposition）

5. JSON:

JavaScript Object Notation 是一种轻量级的数据交换格式，易于人阅读和编写，也易于机器解析和生成，其用于传输结构化数据

数据格式为纯文本，并使用 JSON 编码，无需进行 URL 编码

```JSON
{
	name: 'clown',
	age: 21
}
```

当发送上述 JSON 数据时，应将 HTTP 请求的 Content-Type 头设置为 `application/json`

在实际开发中，选择哪种数据传输方式取决于具体需求、后端 API 的设计和前端的数据处理需求