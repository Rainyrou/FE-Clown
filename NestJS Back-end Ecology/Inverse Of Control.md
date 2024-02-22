后端系统的有以下对象：

- Controller 对象：作为应用入口，接收 HTTP 请求，依赖 Service 处理业务逻辑，返回响应
- Service 对象：封装业务逻辑，依赖 Repository 实现数据的增删改查
- Repository 对象：直接与数据库交互，依赖 DataSource 建立连接，负责数据的持久化操作，如增删改查
* DataSource 对象：管理数据库连接，从 Config 对象中拿到用户名密码等配置信息
* Config 对象：包含配置信息，如数据库连接信息，它们一般在应用启动时加载

```TypeScript
const config = new Config({ username: 'xxx', password: 'xxx' });
const dataSource = new DataSource(config);
const repository = new Repository(dataSource);
const service = new Service(repository);
const controller = new Controller(service);
```

经过一系列初始化后才可使用 Controller 对象，且像 DataSource、Repository、Service、Controller 和 Config 等对象无需每次都 new 一个新对象，即保持单例。在应用初始化时，需要理清依赖的先后关系，创建一大堆对象再组合起来，同时保持对象单例，这导致代码间的高耦合且难以测试，不仅繁琐还容易出错，是后端开发存在的痛点

为解决上述问题，可采用 IOC 的设计原则，其将创建、配置和管理对象的责任从代码中转移到框架或容器中

###### 工作原理

- 开发者在类上声明其依赖，而非通过在类内部实例化来创建
- IOC 容器在应用启动时扫描项目中的类，识别并分析这些声明的依赖关系
- 容器根据其依赖关系自动创建所需的对象实例，并管理它们的生命周期，并将其注入到依赖它们的组件中

###### Dependency Injection 方式

- 依赖通过类的构造函数注入
- 依赖通过公开的属性或 setter 方法注入
- 依赖通过实现特定接口注入

###### 优势

- 组件间无需直接创建彼此的实例，降低耦合度，提高代码的可维护性
- 通过替换依赖的实现，易于进行单元测试

```bash
npx nest new nest-ioc
cd nest-ioc
nest start
```

在浏览器上访问 `http://localhost:3000`  就可以看到 Nest 服务返回的 Hello World

`app.service.ts`

```TypeScript
import { Injectable } from '@nestjs/common';

@Injectable()
export class AppService {
  getHello(): string {
    return 'Hello World';
  }
}
```

`@Injectable()` 标记 `AppService` 类，表示其可注入，Nest 将该类的实例放入 IOC 容器中，以供其他组件注入和使用

`app.controller.ts`

```TypeScript
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()
  getHello(): string {
    return this.appService.getHello();
  }
}
```

`@Controller()` 标记 `AppController` 类，表示其可注入，Nest 将该类的实例放入 IOC 容器中，以供路由处理 HTTP 请求时使用

Service 可被注入也可注入到其他对象，因此用 `@Injectable` 声明。而 Controller 只需被注入，因此 Nest 单独设置 `@Controller`

`appService` 通过依赖注入的方式获取 `AppService` 的实例，如此一来 `AppController` 便可调用 `AppService` 中的方法处理 HTTP 请求，只需声明对 `AppService` 的依赖，就可调用其方法

`app.module.ts`

```TypeScript
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';

@Module({
  imports: [],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

`@Module` 声明模块，其中 `controllers` 是控制器，只能被注入，而 `providers` 可被注入，也可注入到其他对象中

`main.ts`

```TypeScript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```

`NestFactory.create(AppModule)` 用于创建 Nest 应用的实例，并通过 `AppModule` 启动应用。Nest 自动从 `AppModule` 开始解析装饰器声明的依赖关系，并自动创建和组装对象
