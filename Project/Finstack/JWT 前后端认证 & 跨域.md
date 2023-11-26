> [!todo] To do
> 全文背诵

##### 你是如何优化 JWT 认证机制的？在这个过程中遇到的主要挑战是什么？

##### 在处理跨域问题时，你采取了哪些技术措施？这些措施如何有效保证前后端通信的安全性和效率？

#### 多用户鉴权

`config.default.js`

###### CSRF

```JavaScript
config.security = {
    csrf: {
      enable: false,
      ingoreJSON: true
    },
    domainWhiteList: [ '*' ],
  };
```

###### JWT (JSON Web Token)

在配置中我使用了 JWT，这是一个用于身份验证和授权的工具。它是一个 JSON 对象，通常包含用户的一些信息和签名，可以用来在客户端和服务器之间安全地传递信息。JWT 通常与 Bearer Token 一起使用，客户端在每次请求时将 JWT 放入请求头中，服务器解析和验证 JWT 来确定用户的身份和权限

```JavaScript
config.jwt = {
  secret: 'Clown'
};
```

上述配置设置了 JWT 的 secret，用于签名和验证 JWT。对于实际的多用户鉴权实现，需要结合 controller 和 middleware 查看如何使用这个 JWT 配置

###### 数据库

应用连接到 MySQL 数据库，该数据库包含用户的账号、密码和其他关于鉴权的信息

```JavaScript
config.mysql = {
  client: {
    ...
    database: 'clown-pocket'
  },
  ...
};
```

实际的多用户鉴权涉及查询此数据库，验证用户提供的用户名和密码是否匹配，然后发送 JWT 给已验证的用户

###### CORS (跨源资源共享)

CORS 配置允许跨域请求。在多用户环境中需要确保 API 只允许受信任的来源访问，以提高安全性

```JavaScript
config.cors = {
    // 允许所有跨域访问
    origin: '*',
    // 允许 Cookie 跨域
    credentials: true,
    allowMethods: 'GET,HEAD,PUT,POST,DELETE,PATCH'
  };
```

`controller/user.js`

##### 鉴权流程

1. **用户注册** (`register` 方法 in `controller/user.js`):

   - 接收前端传过来的用户名和密码
   - 验证用户名是否已在数据库中存在。如果已存在，则返回错误信息
   - 如果用户名不存在，将用户信息（包括用户名、密码、默认签名、默认头像和注册时间）存入数据库

2. **用户登录** (`login` 方法 in `controller/user.js`):

   - 接收前端传过来的用户名和密码
   - 从数据库中查询该用户名。如果不存在，返回错误信息
   - 如果用户名存在，验证密码是否正确。如果密码错误，返回错误信息
   - 密码正确，则生成一个 JWT。这个 JWT 包含了用户 ID、用户名和该 token 的有效期（24 小时）
   - 返回生成的 JWT 给前端

3. **JWT 的使用和验证**:

   - 当用户尝试访问需要鉴权的 API 时，他们需要在请求头中带上这个 JWT（在 `Authorization` header 里）
   - 服务器会验证这个 JWT 是否有效（如验证签名、检查是否过期等）
   - 如果 JWT 验证通过，服务器会知道这个请求是由哪个用户发出的，因为 JWT 里包含了用户 ID 和用户名

4. **获取用户信息** (`getUserInfo` 方法 in `controller/user.js`):

   - 从请求头中取出 JWT，并验证其有效性
   - 从 JWT 解析出用户名
   - 从数据库中查询该用户名的完整用户信息，然后返回给前端

5. **修改用户信息** (`editUserInfo` 方法 in `controller/user.js`):

   - 和上述获取用户信息流程类似，首先从请求头中取出并验证 JWT
   - 解析 JWT 以获取用户 ID 和用户名
   - 将前端传过来的新信息（如签名、头像等）和数据库中原有的用户信息结合，然后更新数据库

##### 多用户鉴权机制

结合 `controller/user.js`、`service/user.js` 和 `config.default.js` 文件

1. **JSON Web Token (JWT)**

   - 鉴权的核心是 JWT，它是一个编码后的 JSON 字符串，通常包含三部分：头部、载荷和签名。载荷通常包含一些声明（例如用户 ID、用户名），而签名则确保 JWT 的内容没有被篡改
   - 在这个项目中，JWT 使用在 `config.default.js` 中定义的 `secret` 进行签名，确保安全性（呵呵）
   - JWT 的好处~(￣ ▽ ￣)~，服务器不需要保存任何会话或 token 信息。只要客户端的 JWT 有效并且未被篡改，服务器就可以信任其中的内容

2. **使用 JWT 访问受保护的资源**

   - 当用户尝试访问需要鉴权的 API 时，他们必须在请求头的 `Authorization` 字段中附带 JWT
   - 在 `controller/user.js` 中的方法，如 `test`、`getUserInfo` 和 `editUserInfo` ，都需要从请求头中提取 JWT。一旦提取出 JWT，它们使用 `app.jwt.verify` 方法验证 JWT。这个方法会检查 JWT 是否有效，并确保它是使用在 `config.default.js` 中定义的 `secret` 签名的

3. **用户信息的读取和修改**

   - JWT 的好处~(￣ ▽ ￣)~，你可以从 JWT 的载荷中提取用户信息，而不需要再次查询数据库
   - 本项目中，当用户请求自己的信息时，服务器首先从 JWT 中提取用户名，然后查询数据库以获取更多的用户信息。当用户尝试修改自己的信息时，流程大致相同

`middleware/jwtErr.js`

当请求到达 `jwtErr` 中间件时，它会校验此 token 的有效性。如果 token 无效或已过期，用户会得到相应的错误消息。否则请求继续进行

具体工作流程如下：

1. 从请求头中提取 `authorization` 字段作为 token
2. 如果 token 存在且不是'null'，它将尝试使用配置的 `secret` 来验证这个 token
3. 如果验证成功，它将继续执行后续中间件或路由处理函数
4. 如果 token 验证失败，它将响应一个消息表示 token 已过期，并提示用户重新登录
5. 如果 token 不存在或为'null'，它将响应一个消息表示 token 不存在

##### 安全性

- 由于前期玩具项目练手的原因，在很多步骤我都进行了简化，没有使用实际业务那种严谨的安全措施（x

1. JWT 是一个比较安全的机制，但前提是你必须确保 JWT 的 `secret`（在生成和验证 JWT 时使用的密钥）是安全的，且不应该外泄。此外，还需要定期更换此 `secret`。但我的做法显然呵呵~
2. JWT 没有绑定到特定的用户会话，这意味着只要它没有过期，任何持有 JWT 的人都可以访问受保护的资源。为了提高安全性，后续可以考虑加入一些额外措施，详见计网篇
3. 根据 `jwtErr.js`，如果请求中不包含 token，系统会直接返回错误消息，而不继续处理该请求。这是一种好的做法，因为它确保了只有携带有效 token 的请求才能访问受保护的资源
4. 一旦 token 过期，用户需要重新登录以获取新的 token。这增加了安全性，因为它减少了旧 token 被滥用的风险
5. 我在配置中关闭了 CSRF 保护，且开放了 CORS，这当然是不可取的
6. 用户密码是以明文形式存储在数据库中，在实际应用中，应该使用加密算法（如 bcrypt）对密码进行加密后再存储，并在验证时对用户提供的密码进行相同的加密后与存储的加密密码进行比对

###### 用户密码以明文形式存储

从 `controller/user.js` 和 `service/user.js` 代码中可以看到，用户在注册时提交的密码直接存储到了数据库中，没有经过任何形式的加密或哈希处理

```JavaScript
// controller/user.js
async register() {
    ...
    const result = await ctx.service.user.register({
        username,
        password, // 直接使用用户提交的明文密码
        ...
    });
    ...
}

// service/user.js
async register(params) {
    ...
    const result = await app.mysql.insert('user', params); // 将明文密码直接插入到数据库中
    ...
}
```

在现实应用中，我们绝对不会直接存储用户的明文密码！（校校为了保护用户隐私永不存储密码，对于一些必要信息，数据库存储是采用严谨算法，而不同于农专其他 app 有滥用 or 泄露用户信息的风险）而是通过某种算法（例如 SHA256 或 bcrypt）对密码进行哈希处理，然后只存储其哈希值。当用户登录时，系统会再次对提交的密码进行同样的哈希处理，并与数据库中的哈希值进行比较

这样即使数据库被泄露，攻击者也无法直接获得用户的明文密码，从而增加了系统的安全性

#### 跨域

`config.default.js` 

```JavaScript
config.security = {
  csrf: {
    enable: false,
    ignoreJSON: true
  },
  domainWhiteList: [ '*' ], // 注意这里
};
```

本项目使用 Egg 框架的安全配置来处理 CORS，使前端应用能够从不同的源请求后端 API。通过简单地配置 `domainWhiteList`，可以控制哪些域被允许访问 API，从而解决跨域问题（才怪捏）

1. domainWhiteList: Egg 框架中的安全配置，它定义了一个白名单，只有这些列出的域可以访问 API。在配置中，`domainWhiteList` 的值是 `[ '*' ]`，这意味着它允许任何域进行访问，这实际上是开放了 CORS 策略，允许所有来源的请求（x

2. 在 Egg 框架中，这一切都是自动完成的。当在 `config.default.js` 中设置 `domainWhiteList`时，框架将自动处理所有必要的 CORS 响应头。我们无需手动设置 `Access-Control-Allow-Origin` 头或处理预检请求

但只依赖 `domainWhiteList: [ '*' ]` （当然x。在实际生产环境中，当然应该指定具体的域，以确保只有这些域可以访问我们的 API
