HTTP 无状态，一旦完成数据交换，则关闭本次连接，再次交换数据需要重新连接，即服务端无法从连接上跟踪会话，但在某些场景下我们需要维护状态。确定用户身份需跟踪用户的整个会话如使用 Cookie 和 Session，Cookie 通过在客户端记录信息确定用户身份，Session 在服务端中执行类似操作

Cookie 为一小段文本信息，服务端将 Cookie 发送到客户端，客户端存储 Cookie，且同源的每个请求自动携带 Cookie，服务端便可判断用户身份

而对于客户端的每个会话，均有一个唯一的 SESSION ID 与之对应，服务端将用户数据存储于 SESSION ID 对应的文件或内存中，只要客户端每个请求携带 SESSION ID，服务端便可判断用户身份

结合使用 Cookie 与 Session，将 SESSION ID 存储于 Cookie，客户端自动携带同源 Cookie 于请求头以进行会话跟踪，无需在 Cookie 中存储大量信息，无需每次请求均携带 SESSION ID

###### 不同点

- Cookie 将数据存储于客户端，Session 将数据存储于服务端
- Cookie 存储 String 类型，Session 存储 Object 类型
- 在客户端中用户可以修改伪造 Cookie，在服务端中用户无法直接修改伪造 Session
- 由于浏览器的同源策略，Cookie 在同源的情况下才发送，而 Session 在服务端可进行多域共享
- Cookie 大小由客户端控制，一般不超过 4KB，而 Session 大小在服务端中可以灵活控制

###### Cookie 的属性

1. Name 和 Value：Cookie 由 Name 和 Value 的键值对组成，以 `=` 分隔
2. Expires/Max-Age：指定 Cookie 的生命时间，默认为会话级别
3. Domain：指定接收 Cookie 的域，默认向创建 Cookie 的域发送 Cookie
4. Path：指定 Cookie 的有效路径，默认为创建 Cookie 的页面路径
5. Secure：指定 Cookie 只能通过 HTTPS 发送
6. HttpOnly：JavaScript 及其他脚本无法访问 Cookie，防止 XSS 攻击
7. SameSite：防止 CSRF 攻击
