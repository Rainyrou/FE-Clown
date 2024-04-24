HTTP 是无状态的，一旦完成数据交换，就会关闭本次连接，再次交换数据就需要重新连接，即服务端无法从连接上跟踪会话，但在某些场景下我们需要维护状态。确定用户身份需要跟踪用户的整个会话，常用的方式是使用 Cookie 与 Session，简单来说 Cookie 通过在客户端记录信息确定用户身份，Session 是在服务端中执行类似操作

Cookie 实质上是一小段文本信息，服务端将 Cookie 发送到客户端，客户端存储 Cookie，且同源的每个请求自动携带 Cookie，服务端便可判断用户身份

对于客户端的每个会话，都有一个唯一的 SESSION ID 与之对应，服务端将用户数据存储到 SESSION ID 对应的文件或内存中，只要客户端每个请求携带 SESSION ID，服务端便可判断用户身份

仅使用 Cookie 而不使用 Session 进行会话跟踪，服务端将所有用户数据返回给客户端，客户端存储数据，每次请求发送数据到服务端。此种方式理论上可行，但相对不安全，尤其是在用户数据未加密的情况下，若被中间人攻击，用户数据将全部暴露，同时也存在用户自身修改客户端数据、伪造请求或伪造他人身份访问服务端等情况。此外不同浏览器对于同一域 Cookie 的大小与数量存在限制

仅使用 Session 而不使用 Cookie 进行会话跟踪，客户端仅需一个 SESSION ID，因此实现相对简单。通过对所有 URL 拼接成一个 SESSION ID 或对每个表单设置一个隐藏的 input 以存储 SESSION ID，服务端便可进行会话跟踪。由于数据存储在服务端相对安全，且存储量大小完全取决于服务端，因此可以达到更好的效果

现在普遍做法是将 Cookie 与 Session 结合使用，直接将 SESSION ID 存储于 Cookie 中，客户端自动将同源的 Cookie 携带在请求头中进行会话跟踪，这样既不需要在 Cookie 中存储大量信息，也无需每次请求都附带 SESSION ID

###### 不同点

- Cookie 将数据存储在客户端，Session 将数据存储在服务端
- Cookie 存储 String 类型，Session 存储 Object 类型
- 在客户端中用户可以修改伪造 Cookie，在服务端中用户无法直接修改/伪造 Session
- 由于浏览器的同源策略，Cookie 只在同源的情况下才会发送，而理论上，Session 在服务端可进行多域共享
- Cookie 大小由客户端限制，一般不超过 4KB，而在服务端中 Session 大小可以灵活控制

###### Cookie 的属性

1. Name 和 Value：`Cookie` 的基本组成部分是其名称和值，它们是键值对，以 `=` 分隔，如 `username=JohnDoe`
2. Expires/Max-Age：指定 `Cookie` 的生存时间，默认情况下，`Cookie` 是会话级的，只存在于用户的会话期间。当指定 `Expires` 或 `Max-Age` 属性后，`Cookie` 会在指定的时间段后过期

   - `Expires` 设置一个特定的过期日期和时间
   - `Max-Age` 设置一个特定的存活秒数

3. Domain：指定可以接收 `Cookie` 的域，默认情况下，`Cookie` 只发送给创建它的域
4. Path：指定 `Cookie` 在哪些路径下有效，默认情况下，它只在创建 `Cookie` 的页面路径下有效
5. Secure：指定 `Cookie` 只能通过安全的 HTTPS 连接发送
6. HttpOnly：当设置这个属性，JavaScript 和其他脚本无法访问该 `Cookie`，可防止 XSS 攻击
7. SameSite：用于防止 CSRF，它可以有三个值：
   - `Strict`：仅在同一站点请求中发送 `Cookie`
   - `Lax`：顶级导航和从外部站点发送的 GET 请求可发送 `Cookie`
   - `None`：无论是否跨域，所有请求都会发送 `Cookie`，但必须与 `Secure` 标志结合使用
