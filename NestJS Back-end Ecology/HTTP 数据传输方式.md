##### Nest 实现

```bash
nest new Nest-demo
cd Nest-demo
nest g resource clown
nest start --watch
```

API 接口跑通：

![[1695863639627.png]]

![[1695863765587.png]]

`main.ts` 负责启动 Nest 的 IOC 容器，调用 `useStaticAssets` 以支持请求静态资源：

```TypeScript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { NestExpressApplication } from '@nestjs/platform-express';

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);
  app.useStaticAssets('public', { prefix: '/static' });
  await app.listen(3000);
}
bootstrap();
```

- 当选择 Express 作为 HTTP 平台时，可使用 `NestExpressApplication` 以获得特定于 Express 的方法和属性
- 异步函数 `bootstrap` 是 Nest 应用的启动函数
- `public` 目录提供静态资源，所有资源的 URL 将以 `/static` 为前缀。若有一个在 `public/images/logo.png` 的文件，可通过 `/static/images/logo.png` 访问

在静态文件目录 `public` 下添加一个 `index.html`：

```html
<html>
    <body>
        Hello Nest!!!
    </body>
</html>
```

重启服务，访问浏览器：

![[1695864625476.png]]

###### URL Param

在原先自动生成的 CRUD 代码基础上小幅度修改

`clown.controller.ts`

```TypeScript
@Controller('api/clown')
export class ClownController {
  @Get(':id')
  urlParam(@Param('id') id: string) {
    return `received: id=${id}`;
  }
}
```

`@Controller('api/clown')` 定义一个控制器类，它的路由前缀是 `api/clown`，在这个控制器中定义的路由都以 `/api/clown` 为前缀

`@Get(':id')` 是一个路由装饰器，用于将一个 GET 请求路由到 `urlParam` 方法

`@Controller('api/clown')` 的路由拼接 `@Get(':id')`，即 `/api/clown/:id`，表示此控制器方法将处理如 `/api/clown/250` 的 GET 请求，其中 `250` 是一个参数

`urlParam(@Param('id') id: string)`：使用 `@Param('id')` 装饰器从 URL 中提取 `id` 参数，并将其注入到控制器的方法中

`public/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <script src="https://unpkg.com/axios@0.24.0/dist/axios.min.js"></script>
</head>
<body>
    <script>
        async function urlParam() {
            const res = await axios.get('/api/clown/250');
            console.log(res);
        }
        urlParam();
    </script>
</body>
</html>
```

启动服务，访问浏览器，控制台打印服务端返回的消息，证明服务端拿到了通过 urlParam 传递的数据

![[1695896180520.png]]

###### Query

`clown.controller.ts`

```TypeScript
@Controller('api/clown')
export class ClownController {
  @Get('find')
  query(@Query('name') name: string, @Query('age') age: number) {
    return `received: name=${name},age=${age}`;
  }
}
```

`public/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <script src="https://unpkg.com/axios@0.24.0/dist/axios.min.js"></script>
</head>
<body>
    <script>
        async function query() {
            const res = await axios.get('/api/clown/find', {
                params: {
                    name: '小丑',
                    age: 21
                }
            });
            console.log(res);
        }
        query();
    </script>
</body>
</html>
```

注意这个 `find` 的路由要放到 `:id` 的路由前面，因为 Nest 是从上往下匹配的，如果放在后面，那就匹配到 `:id` 的路由了

![[1695898521734.png]]

测试，服务端成功接收我们通过 query 传递的数据

![[1695897173125.png]]

###### Form-urlencoded

`dto/create-clown.dto.ts`

```TypeScript
export class CreateClownDto {
    name: string;
    age: number;
}
```

该文件定义了一个 data transfer object，用于封装将通过 POST 请求发送到后端的数据

`clown.controller.ts`

```TypeScript
import { CreateClownDto } from './dto/create-clown.dto';
@Controller('api/clown')
export class ClownController {
  @Post()
  body(@Body() createClownDto: CreateClownDto) {
    return `received: ${JSON.stringify(createClownDto)}`;
  }
}
```

`@Post()` 用于将 POST 请求路由到 `body` 方法，`@Body()` 用于从请求体中提取数据并注入到 `createClownDto` 参数中。`createClownDto` 参数的类型是 `CreateClownDto`，Nest 解析请求体，并将数据填充到 DTO 对象中。然后，`createClownDto` 对象被 JSON 化并作为响应返回

`public/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <script src="https://unpkg.com/axios@0.24.0/dist/axios.min.js"></script>
    <script src="https://unpkg.com/qs@6.10.2/dist/qs.js"></script>
</head>
<body>
    <script>
        async function formUrlEncoded() {
            const res = await axios.post('/api/clown', Qs.stringify({
                name: '小丑',
                age: 21
            }), {
                headers: { 'content-type': 'application/x-www-form-urlencoded' }
            });
            console.log(res);
        }
        formUrlEncoded();
    </script>
</body>
</html>
```

导入 `qs` 库，用于处理 URL 编码
`Qs.stringify` 将包含 `name` 和 `age` 属性的对象转换为 `x-www-form-urlencoded` 格式

服务端再次成功接收数据：

![[1695899168882.png]]

###### JSON

`clown.controller.ts` 无需任何改动，Form-urlencoded 和 JSON 均从 body 取值，Nest 内部会根据 content-type 进行区分，使用不同的解析方式

`public/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <script src="https://unpkg.com/axios@0.24.0/dist/axios.min.js"></script>
</head>
<body>
    <script>
        async function json() {
            const res = await axios.post('/api/clown', {
                name: '小丑',
                age: 21
            });
            console.log(res);
        }
        json();
    </script>
</body>
</html>
```

使用 axios 发送 POST 请求，默认传输 JSON 即指定 content-type 为  `application/json`，无需手动指定

服务端再次成功的接收数据：

![[1695899730122.png]]

###### Form-data

`clown.controller.ts`

```TypeScript
import { Controller, Get, Post, Body, Patch, Param, Delete, Query, UploadedFiles, UseInterceptors } from '@nestjs/common';
import { AnyFilesInterceptor } from '@nestjs/platform-express';

@Controller('api/clown')
export class ClownController {
  @Post('file')
  @UseInterceptors(AnyFilesInterceptor({
    dest: 'uploads/'
  }))
  body2(@Body() createClownDto: CreateClownDto, @UploadedFiles() files: Array<Express.Multer.File>) {
    console.log(files);
    return `received: ${JSON.stringify(createClownDto)}`;
  }
}
```

`@UseInterceptors` 是 Nest 的拦截器装饰器，它使用文件上传的拦截器 `AnyFilesInterceptor`，上传的文件将保存在 `uploads/` 目录下

`body2` 方法使用 `@Body()` 获取非文件数据，使用 `@UploadedFiles()` 获取上传的文件

在命令行执行：

```bash
npm i -D @types/multer
```

`public/index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <script src="https://unpkg.com/axios@0.24.0/dist/axios.min.js"></script>
</head>
<body>
    <input id="fileInput" type="file" multiple />
    <script>
        const fileInput = document.querySelector('#fileInput');

        async function formData() {
            const data = new FormData();
            data.set('name', '小丑');
            data.set('age', 20);
            data.set('file1', fileInput.files[0]);
            data.set('file2', fileInput.files[1]);

            const res = await axios.post('/api/clown/file', data, {
                headers: { 'content-type': 'multipart/form-data' }
            });
            console.log(res);
        }

        fileInput.onchange = formData;
    </script>
</body>
</html>
```

在浏览器上测试：

![[1695901690792.png]]

![[1695901916552.png]]

在控制台可看到，服务端成功接收到了我们上传的文件

![[1695902393521.png]]

![[1695902336962.png]]
