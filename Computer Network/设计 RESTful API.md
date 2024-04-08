1. HTTP Method 明确意图
2. HTTP 状态码响应状态
3. 命名 & URL/URI 设计： URL/URI 使用复数形式如 `/users`， 层级关系通过路径表达如 `/users/{id}/orders`，避免 URL/URI 中的 CRUD 动词，行为应通过 HTTP 方法表达
4. 使用查询参数实现分页、过滤、排序，如 `/users?offset=10&limit=5`
5. 强缓存 & 协商缓存
6. 安全性：使用 HTTPS 和认证授权机制如 OAuth

