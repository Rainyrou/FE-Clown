通过 Mobile 端扫码实现 PC 端登录，本质为认证状态迁移 + 跨设备授权的机制：

* PC 浏览器向服务端请求登录二维码，服务端接收到请求后，生成全局唯一且有时效性的 UUID 将并其作为 Key，在 Redis 缓存中创建记录并设置 UUID 初始状态为待扫描和相应过期时间，将 UUID 包装为 URL 并生成对应二维码图片，将其返回给 PC 端浏览器
* PC 端浏览器接收到二维码后将其展示给用户并通过与服务端建立 WebSocket 连接以实时获取二维码状态，Mobile 端扫描二维码并解析，获取 UUID 后向服务端发送"已扫描"请求（UUID + AuthToken）
* 服务端接收到请求后验证 UUID + AuthToken 以确认用户身份，根据 UUID 查找 Redis 缓存对应记录，若其为待扫描状态且未过期，更新 UUID 状态为已扫描并关联相关 Mobile 端，PC 端通过 WebSocket 连接获取 UUID 状态为已扫描，重新渲染 UI 并显示"扫描成功"，Mobile 端在请求成功后弹出确认弹窗，显示"即将登录 PC 端"，点击"确认登录"后向服务端发送"确认登录"请求（UUID + AuthToken）
* 服务端接收到请求后，再次验证 UUID + AuthToken，若其为已扫描状态，则为 PC 端浏览器生成新 AuthToken，更新 UUID 状态为已确认并将新 AuthToken 存储于 UUID 记录，返回新 AuthToken 给 PC 端浏览器，PC 端浏览器通过 WebSocket 连接获取 UUID 状态为已确认，接收新 AuthToken 并存储于 Cookie，跳转至已登录页面，后续所有请求均携带该新 AuthToken

