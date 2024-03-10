###### 获取占用端口的进程 ID

以管理员权限运行 PowerShell

```bash
$connection = Get-NetTCPConnection -LocalPort 3306
$process = Get-Process -Id $connection.OwningProcess
Write-Host "Process ID: $($process.Id)"
Write-Host "Process Name: $($process.ProcessName)"
Stop-Process -Id 6044 -Force
```

- 使用 `Get-NetTCPConnection` 来查找监听特定端口如 3306 的进程。该命令默认不显示 PID，但你可通过查找与该连接相关联的进程来获取 PID
- 使用 `Get-Process` 来查找与这个连接相关联的进程。由于 `Get-NetTCPConnection` 不直接提供 PID，需要通过 `OwningProcess` 属性来获取它，这个属性实际上是 PID
- 这将输出监听在 3306 端口上的进程的 ID 和名称
* 终止 PID 为 6044 的进程

###### mysql2

```bash
mkdir mysql2-test
cd mysql2-test
npm i -y
npm i --save mysql2
```

`index.js`

```JavaScript
const mysql = require("mysql2");
const connection = mysql.createConnection({
  host: "localhost",
  port: 3306,
  user: "root",
  password: "clown",
  database: "practice",
});

connection.query("SELECT * FROM customers", function (err, results, fields) {
  console.log(results);
  console.log(fields.map((item) => item.name));
});
```

```bash
node ./index.js
```

![[1708185249261.png]]

add:

```JavaScript
connection.execute(
  "INSERT INTO customers (name) VALUES (?)",
  ["小丑"],
  (err, results, fields) => {
    console.log(err);
  }
);
```

delete:

```JavaScript
connection.execute("DELETE  FROM customers where name=?", ["雨"], (err) =>
  console.log(err)
);
```

modify:

```JavaScript
connection.execute('UPDATE customers SET name="雨" where name="小丑"', (err) =>
  console.log(err)
);
```

连接池：

```JavaScript
const mysql = require("mysql2/promise");

(async function () {
  const pool = mysql.createPool({
    host: "localhost",
    user: "root",
    password: "clown",
    database: "practice",
    waitForConnections: true,
    connectionLimit: 10,
    maxIdle: 10,
    idleTimeout: 60000,
    queueLimit: 0,
    enableKeepAlive: true,
    keepAliveInitialDelay: 0,
  });

  const [results] = await pool.query("select * from customers");
  console.log(results);
})();
```

![[1708186269929.png]]

手动获取：

```JavaScript
const connection = await pool.getConnection();
  const [results] = await connection.query("select * from orders");
  console.log(results);
```

- 连接池预先创建一定数量的数据库连接并保持活跃状态，减少每次操作时创建和销毁连接的开销
- 通过限制并发连接数，防止过多的并发请求耗尽服务端资源
- 连接池可以自动处理连接错误，重新建立连接等

对象关系映射（Object-Relational Mapping，简称 ORM）用于实现面向对象编程语言与关系数据库的数据之间的转换

- 开发者可以使用他们熟悉的编程语言来操作数据库，而无需直接书写 SQL 语句，提高生产力，加快开发速度
- 通过参数化查询和 ORM 框架本身的设计，可以减少 SQL 注入的风险
- 应用程序代码可在不同的数据库系统之间迁移，而无需重写 SQL 语句
- 使用面向对象的方式操作数据库，提高了代码的可读性和可维护性

###### ORM 的工作原理

- 映射：ORM 框架将数据库表映射为应用程序中的类，将记录映射为对象实例，将字段映射为对象属性
- 数据库操作：开发者通过操作对象和类来进行数据库的 CRUD，ORM 框架负责将这些操作转换为对应的 SQL 语句并执行
- 数据类型转换：ORM 还负责转换应用程序数据类型和数据库数据类型之间的差异

新建项目：

```bash
npx typeorm@latest init --name typeorm-test --database mysql
cd typeorm-test
npm install --save mysql2
```

`data-source.ts`

```TypeScript
import "reflect-metadata";
import { DataSource } from "typeorm";
import { User } from "./entity/User";

export const AppDataSource = new DataSource({
  type: "mysql",
  host: "localhost",
  port: 3306,
  username: "root",
  password: "clown",
  database: "practice",
  synchronize: true,
  entities: [User],
  migrations: [],
  subscribers: [],
  connectorPackage: "mysql2",
  extra: {
    authPlugin: "sha256_password",
  },
});
```

在命令行执行：

```bash
npm run start
```

数据库多了一张表：

![[1708187707263(1).png]]

![[1708187782401(1).png]]

`src/index.ts`

```TypeScript
import { AppDataSource } from "./data-source";
import { User } from "./entity/User";

AppDataSource.initialize()
  .then(async () => {
    console.log("Inserting a new user into the database...");
    const user = new User();
    user.firstName = "Timber";
    user.lastName = "Saw";
    user.age = 25;
    await AppDataSource.manager.save(user);
    console.log("Saved a new user with id: " + user.id);
    console.log("Loading users from the database...");
    const users = await AppDataSource.manager.find(User);
    console.log("Loaded users: ", users);
    console.log(
      "Here you can setup and run express / fastify / any other framework."
    );
  })
  .catch((error) => console.log(error));
```

- `AppDataSource` 是 TypeORM 数据源的配置，它包含数据库连接的详细信息，`User` 是一个实体类，代表数据库中的一个表，每个实例对象对应表中的一行数据
- 调用 `AppDataSource.initialize()` 初始化数据源，即建立数据库连接，该方法返回一个 Promise
- 创建一个新的 `User` 实例并设置其属性，`save` 方法会生成相应的 `INSERT` 或 `UPDATE` SQL 语句，根据实体的当前状态（新建或已存在）来保存数据
* 调用 `AppDataSource.manager.find(User)` 查询并加载 `User` 表中的所有行，`find` 方法生成并执行一个 `SELECT` SQL 查询，然后将结果映射回实体对象

