[POST - HTTP | MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST)

[rest - What is the difference between POST and PUT in HTTP? - Stack Overflow](https://stackoverflow.com/questions/630453/what-is-the-difference-between-post-and-put-in-http)

[Site Unreachable](https://stackoverflow.com/questions/549/the-definitive-guide-to-form-based-website-authentication)

##### GET

- `GET` 方法用于请求资源。当我们通过浏览器访问一个网页时，浏览器就向服务器发出一个 `GET` 请求，要求服务器返回该网页的 HTML 内容。这就是一个典型的 `GET` 请求的用例
- `GET` 请求通常用来获取资源信息，而不是修改或添加资源。`GET` 请求的数据附加在 URL 之后，因此数据不应该通过请求体发送，即 `GET` 请求通常没有请求体（payload/body）
- 尽管 HTTP 规范并没有明确禁止 `GET` 请求包含请求体（有效载荷），但在实际使用中，大多数服务器、代理或客户端都不期望 `GET` 请求包含请求体。因此，即使技术上可以发送带有请求体的 `GET` 请求，它也可能会被某些服务器拒绝，或者请求体可能会被忽略
- 请求是否有主体：否
- 成功的响应是否有主体：是
- 安全：是
- 幂等：是
- 可缓存：是

* 当一个 `GET` 请求发出时，客户端会向指定的服务器发送一个 HTTP 请求。这个请求包含一个请求行（例如：`GET /index.html HTTP/1.1`），还可能包含一些请求头部，但通常不包含请求体
* 服务器收到这个请求后，会查找请求的资源，并将资源的内容（或资源的表示）作为响应体发送回客户端

使用 `Fetch API` 发出 `GET` 请求：

```HTTP
fetch('/example')
	.then(response => response.json())
	.then(data => console.log(data))
	.then(error => console.error('Error', error));
```

使用 `Fetch API` 向 `/example` 路径发送一个 `GET` 请求，期望服务器返回 JSON 格式的响应。当接收到服务器的响应后，我们解析这个 JSON 数据，并将其输出到控制台

##### POST

- `POST` 是一个 HTTP 方法，常用于提交表单或者上传文件。在 `POST` 请求中，数据包含在请求体中，而不是 URL 中。服务器读取这些数据，并根据这些数据执行相应的操作
- 当你发送一个请求时，你的客户端会告诉服务器你正在发送什么样的数据。这是通过 `Content-Type` 头完成的。例如，当你提交一个表单时，浏览器需要告诉服务器它正在发送编码格式为 `application/x-www-form-urlencoded` 或 `multipart/form-data` 的数据

* `PUT` 请求一般用于更新资源，它是幂等的，即多次发送同一请求，资源的状态是相同的，没有副作用。`POST` 请求则可能产生副作用，例如多次提交同一订单可能会导致订单被重复处理

- `POST` 请求通常是通过 HTML 表单发送的。用户填写一个 HTML 表单，然后点击提交按钮，浏览器就会生成一个 `POST` 请求，将表单数据发送到服务器

* 请求是否有主体：是
* 成功的响应是否有主体：是
* 安全：否
* 幂等：否
* 通常 `POST` 请求的响应不被缓存。但如果响应明确地包含了足够新的信息（通过相应的 HTTP 缓存头字段指示），那么响应是可以被缓存的

在 HTTP 术语中，"安全"是指请求不应对资源产生副作用

`POST` 请求的一些典型用途：

- 对现有资源进行注释
- 在公告板、新闻组、邮件列表或类似的文章组中发布消息
- 通过注册模板新增用户
- 向数据处理过程提供一批数据（例如提交一个表单）
- 通过追加操作，扩展数据库数据

`Content-Type` 选项

- `application/x-www-form-urlencoded`：这是表单提交时最常用的一种方式。数据被编码成键值对，每个键值对之间用 `&` 分隔，键与值之间用 `=` 分隔。例如: `username=JohnDoe&age=30`
- `multipart/form-data`：这种类型常用于需要上传文件的表单。它每一部分都包含一个不同的信息块，这些块由一串边界标识符分隔开
- `text/plain`：未加处理的纯文本数据

###### 底层原理

- 当一个 HTML 表单设置 `method="POST"` 和 `enctype="application/x-www-form-urlencoded"` 后，提交该表单会生成一个 `POST` 请求

```HTML
<form action="/submit" method="POST" enctype="application/x-www-form-urlencoded">
	<input type="text" name="username" />
	<input type="password" name="password" />
	<button type="submit">Submit</button>
</form>
```

当用户填写完表单并点击 `Submit` 按钮后，浏览器会生成一个类似 `username=JohnDoe&password=123456` 这样的请求体，并将该请求体发送到服务器的 `/submit` 路径

- 当表单需要上传文件时，`enctype` 应设置为 `multipart/form-data`

```HTML
<form action="/upload" method="POST" enctype="multipart/form-data">
	<input type="file" name="myFile" />
	<button type="submit">Upload</button>
</form>
```

此种情况下，浏览器会将文件编码为一组数据，每组数据用一个"boundary"分隔

除 HTML 表单之外，还可通过多种方式发送 `POST` 请求，其中最常见的是使用 `XMLHttpRequest`（在新标准中常使用 `Fetch API`）。在这些情况下，请求主体（即发送给服务器的数据）可以是各种不同的类型，例如 `JSON`, `XML`, `Binary Data` 等

- 使用 `Fetch API` 发送一个 JSON 对象到服务器：

```JavaScript
fetch('/api/resource', {
	method: 'POST',
	headers: {
		'Content-Type': 'application/json'
	},
	body: JSON.stringify({
		key1: 'value1',
		key2: 'value2'
	})
});
```

- 使用默认的  `application/x-www-form-urlencoded`  作为 content type 的表单：

```HTTP
POST /test HTTP/1.1
Host: foo.example
Content-Type: application/x-www-form-urlencoded
Content-Length: 27

field1=value1&field2=value2
```

- `/test`：请求的 URL 路径
- `Host: foo.example`：指定服务器域名
- `Content-Length: 27`：告诉服务器请求体的长度（以字节为单位）

* 请求体包含了要发送到服务器的数据，这些数据以键值对的形式发送

* 使用  `multipart/form-data`  作为 content type 的表单：

```HTTP
POST /test HTTP/1.1
Host: foo.example
Content-Type: multipart/form-data;boundary="boundary"

--boundary
Content-Disposition: form-data;name="field1"

value1
--boundary
Content-Disposition: form-data;name="field2";filename="example.txt"

value2
--boundary--
```

- `Content-Type` 部分定义了一个边界字符串 `boundary`
- 请求体包含多个不同的数据段，每个数据段都以 `--boundary` 开始
- 每个数据段包含一个 `Content-Disposition` 头，它描述了数据的名字（`name`属性）和文件名（`filename`属性）
- 每个数据段的头和数据之间有一个空行
- 请求体以 `--boundary--` 结束，表示所有数据段已发送完

