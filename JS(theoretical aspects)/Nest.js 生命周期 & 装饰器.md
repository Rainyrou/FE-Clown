生命周期：

| Lifecycle hook method       | Lifecycle event triggering the hook method call                                                                                                                                                              |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `onModuleInit`              | Called once the host module's dependencies have been resolved. <br>                                                                                                                                          |
| `onApplicationBootstrap`    | Called once all modules have been initialized, but before listening for connections. <br>                                                                                                                    |
| `onModuleDestroy`           | Called after a termination signal (e.g., `SIGTERM`) has been received. <br>                                                                                                                                  |
| `beforeApplicationShutdown` | Called after all `onModuleDestroy` handlers have completed (Promises resolved or rejected); <br>once complete (Promises resolved or rejected), all existing connections will be closed (`app.close` called). |
| `onApplicationShutdown`     | Called after connections close (`app.close` resolves).                                                                                                                                                       |

1. 执行绑定于特定路由的中间件
2. Guards 决定是否继续处理当前请求
3. Interceptors 可在请求处理前执行特定逻辑
4. Pipes 验证并转换请求数据
5. Controller 处理请求并调用 Service 执行特定逻辑
6. Interceptors 可在响应返回前执行特定逻辑
7. 上述过程中 Exception Filters 捕获并处理抛出的异常

![[Pasted image 20240214121024.png]]

`@Module` 封装 controller 和 service 为可复用单元

- `controllers`：当前 module 对应的 controller
- `providers`：当前 module 内可用的 service
- `imports`：所依赖的外部 module
- `exports`：导出的内部 module

| 装饰器   | 数据来源     | 类型             | 场景              |
| -------- | ------------ | ---------------- | ----------------- |
| `@Body`  | 请求 Body    | JSON / Form-data | POST/PUT 提交表单 |
| `@Param` | URL 路径参数 | 字符串           | RESTful 资源标识  |

从  `ConfigService`  实例而非直接从  `ConfigService`  类获取配置信息是因为 Nest.js 以依赖注入机制组织代码，将对象的创建和依赖关系的管理从业务代码中分离出来，`ConfigService` 类属于 provider，当应用启动并调用  `NestFactory.create(AppModule)`  时，Next.js 创建依赖注入容器，其负责创建和管理所有 Provider 实例，在解析  `AppModule`  时，Nest.js 遍历  `AppModule`  及其子 Module 中声明的 provider，包括  `ConfigService`，Nest.js 根据 provider 的注册方式如  `@Injectable`  决定如何创建和管理其实例，`ConfigService`  类被注册到该容器中，当使用  `app.get(ConfigService)`  时，实际上是在向依赖注入容器请求一个  `ConfigService`  实例，容器确保每次请求获取的均为同一实例（单例模式），若直接从  `ConfigService`  类获取配置信息则绕过依赖注入机制
