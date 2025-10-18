通过 Mobile 端扫码实现 PC 端登录，本质为认证状态迁移 + 跨设备授权的机制，其解决的核心问题为如何安全便捷地将 Mobile 端已验证的登录状态转移至待验证的 PC 端浏览器而无需重新验证

1. PC 浏览器向服务端发送"请求登录二维码"的请求，服务端接收到请求后，生成全局唯一且有时效性的 UUID，将其作为 Key，在后端 Redis 存储中创建记录并设置 UUID 初始状态为待扫描和过期时间，服务端将该 UUID 包装为 URL 并生成对应二维码图片，将其返回给 PC 端浏览器
2. PC 端浏览器接收到二维码后将其展示给用户并通过与服务端建立 WebSocket 连接以实时获得二维码状态变化，Mobile 端扫描二维码并解析，获取 UUID 后向服务端发送"已扫描"的请求（UUID + AuthToken）
3. 服务端接收到请求后验证 AuthToken 的有效性以确认用户身份，根据 UUID 查找 Redis 对应记录，确认其为待扫描状态且未过期，服务端更新 UUID 状态为已扫描并关联相关机型设备，PC 端通过 WebSocket 连接发现 UUID 状态为已扫描，服务端返回 Mobile 端相关元数据，PC 浏览器重新渲染 UI，显示"扫描成功，请在手机上确认登录"，Mobile 端在请求成功后弹出确认弹窗，显示"即将登录 PC 端"，点击"确认登录"后向服务端发送"确认登录"的请求（UUID + AuthToken）
4. 服务端接收到请求后，再次验证 UUID + AuthToken，确认已扫描状态且 AuthToken 对应用户与 Redis 记录的 UserID 一致，服务端为 PC 端浏览器生成新的 AuthToken，更新 UUID 状态为已确认并将 PC 浏览器新的 AuthToken 存储于 UUID 的记录中，PC 端浏览器通过 WebSocket 连接发现 UUID 状态为已确认，服务端返回新的 AuthToken，PC 端浏览器接收并存储于 Cookie 中，PC 端浏览器跳转至已登录页面，后续所有 PC 端浏览器请求均携带该新的 AuthToken
