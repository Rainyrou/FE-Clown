HTTP 协议是无状态的，但在某些场景下，我们需要维护状态。确定用户身份就需要跟踪用户的整个会话，最常用的会话跟踪是使用 Cookie 与 Session，简单来说 Cookie 通过在客户端记录信息确定用户身份，Session 通过在服务器端记录信息确定用户身份

HTTP 协议是无状态的，一旦数据交换完毕，此次链接就会关闭，再次交换数据就需要重新连接，即服务器无法从链接上跟踪会话。服务端为进行会话跟踪，给每个客户端颁发一个通行证，客户端访问必须携带通行证，这样服务端就能区别用户身份了。Cookie 实际上是一小段文本信息，服务端将 Cookie 发送到浏览器，浏览器将 Cookie 存储起来，并且对于同源的每个请求都会自动携带 Cookie(CSRF 跨站请求伪造正是基于此策略)，于是服务端就可以判断用户身份

Session 是服务器端使用的一种记录客户端状态的机制，相应的也增加了服务器的存储压力。 对于客户端的每个会话，都有一个唯一的 SESSION ID 与之对应，服务端将用户数据存储进 SESSION ID 对应的文件或者内存中，只要客户端每次请求将 SESSION ID 交予服务端，服务端就能区别用户，从而进行会话跟踪

###### 实例

仅使用 Cookie 而不使用 Session 进行用户身份跟踪，服务端将所有用户数据信息告知浏览器，浏览器进行存储，每次请求将数据发送到服务端。此种方式理论上可行，但相对并不安全，尤其是在用户数据信息未加密的情况下，若是被中间人攻击，则用户的数据信息将全部暴露，也存在用户自身修改浏览器数据或伪造请求，伪造他人身份访问服务器等情况。此外不同浏览器对于同一域 Cookie 的大小与数量都有限制，将用户数据都存储于 Cookie 可能会有空间或数量不够的情况

仅使用 Session 而不使用 Cookie 进行用户身份跟踪，由于使用 Session 在客户端仅需要一个 SESSION ID 传输到服务端就能进行会话跟踪，因此实现相对简单。可以通过对所有的 URL 拼接成一个 SESSION ID 或者对于每个表单设置一个隐藏的 input 用以存储 SESSION ID，服务器就可以进行会话跟踪。由于数据是存储在服务端，相对安全，且存储量大小完全取决于服务端，可以较好控制

现在普遍使用的方式就是将 Cookie 与 Session 结合使用，直接将 SESSION ID 存储于 Cookie 中，浏览器自动将同源的 Cookie 携带在请求头中，进行会话跟踪，这样既不需要在 Cookie 中存储大量信息，也不需要每次请求都附带 SESSION ID

###### 不同点

- Cookie 将数据存储在浏览器，Session 将数据存储在服务端
- Cookie 存储 String 类型，Session 存储 Object 类型
- 在客户端中用户可以修改伪造 Cookie，在服务端中用户无法直接修改伪造 Session
- 由于浏览器的同源策略，Cookie 只有同源的情况下才会发送，而理论上，Session 在服务端可以进行多域共享
- Cookie 大小由浏览器限制，一般不超过 4KB，而在在服务端中 Session 大小可以灵活控制

###### Cookie 的属性

1. Name 和 Value：`Cookie` 的基本组成部分是其名称和值，它们是键值对，可以使用 `=` 分隔，如 `username=JohnDoe`

2. Expires/Max-Age：指定 `Cookie` 的生存时间。默认情况下，`Cookie` 是会话级的，只存在于用户的会话期间。当指定了 `Expires` 或 `Max-Age` 属性后，`Cookie` 会在设定的日期或时间段后过期

   - `Expires` 设置一个特定的过期日期和时间
   - `Max-Age` 设置一个特定的存活秒数

3. Domain：指定可以接收 `Cookie` 的域。默认情况下，`Cookie` 只发送给创建它的域

4. Path：指定 `Cookie` 在哪些路径下有效。默认情况下，它只在创建 `Cookie` 的页面路径下是有效的

5. Secure：指定 `Cookie` 只能通过安全的 HTTPS 连接发送

6. HttpOnly：当设置了这个属性，JavaScript 和其他脚本将无法访问该 `Cookie`。防止 XSS 攻击，即使攻击者可以执行某些恶意脚本，他们也无法访问带有此标志的 `Cookie`

7. SameSite：用于防止 CSRF。它可以有三个值：
   - `Strict`：`Cookie` 仅在同一站点请求中发送
   - `Lax`：在顶级导航和从外部站点发送的 GET 请求中，`Cookie` 会被发送
   - `None`：在所有请求中，无论是否跨站点，`Cookie` 都会被发送。但必须与 `Secure` 标志一起使用
