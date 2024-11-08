1. 进程守护：PM2 可确保项目持续运行，应用崩溃后自行重启，可随时停止和重启进程，PM2 守护进程长期运行在后台，启动时其将自身作为一个独立后台进程运行，根据配置并利用 Node.js 的 `child_process` 模块来 fork 出新的子进程并通过事件监听和进程通信持续监控这些进程的生命周期，若进程异常退出，PM2 守护进程监听到 `exit` 事件并根据配置决定是否自动重启
2. 负载均衡：PM2 负载均衡机制基于操作系统级的负载均衡，即通过操作系统的调度机制来分配 TCP/HTTP 连接到不同进程，PM2 利用 Node.js 的 Cluster 模块自动进行负载均衡，只需简单配置，PM2 就能自动检测服务器的 CPU 核心数并默认为每一核心启动一个实例，守护进程负责维护一个负载均衡器，将请求平均分配给所有正运行的实例，项目便可以集群模式跨多核心服务器运行，最大化地利用 CPU，提高应用吞吐率和稳定性
3. 日志管理：PM2 自动捕获并保存项目的标准输出 stdout 和标准错误输出 stderr，便于追踪问题来源和调试
4. 性能监控：PM2 提供实时监控如 CPU 和内存的使用情况，在此基础上集成第三方监控工具做进一步分析
5. 指定环境变量：PM2 允许为项目指定环境变量，便于在开发、测试和生产中运行

###### 处理异常退出

1. 实时查看应用日志

```bash
pm2 logs
pm2 logs myApp/myId # 指定应用名或 id
```

实时跟踪日志输出，显示最新的 1000 行日志并实时更新：

```bash
pm2 logs --lines 1000
```

2. 实时监控 Node.js 应用的 CPU 和内存使用情况：

```bash
pm2 monit
```

3. 自动重启和零停机更新

```bash
pm2 reload myApp
```

4. 日志旋转，自动切割日志文件，避免单个日志文件过大，安装并配置日志旋转的相关参数如最大文件大小、保留的文件数量等

```bash
pm2 install pm2-logrotate
pm2 set pm2-logrotate:max_size 100M 
pm2 set pm2-logrotate:retain 10
```

###### 配置 ssh

```bash
sudo su -
yum install openssh-server
systemctl start sshd
systemctl enable sshd
sudo systemctl status sshd
ssh-keygen -t rsa -b 4096 -C "yourname@gmail.com" # 一路回车
cat id_rsa.pub # 配置到 GitHub ssh密匙
touch /root/.ssh/config
vim /root/.ssh/config # 配置如下（不懂看文档最佳，其次 StackOverflow）
```

`.ssh/config`

```
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa
```

```bash
ssh -T git@github.com
```

###### 安装 MySQL

```bash
yum update
rpm -Uvh http://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
yum install mysql-community-server
systemctl start mysqld
systemctl enable mysqld
mysql --version
grep 'temporary password' /var/log/mysqld.log # 获取临时密码
mysql -u root -p # 登录 MySQL
ALTER USER 'root'@'localhost' IDENTIFIED BY 'your_new_password'; # 修改 root 密码
FLUSH PRIVILEGES; # 密码更改立即生效
exit; # 退出 MySQL
mysql -u root -p
```

打开腾讯云 3306 安全组

本地数据库连接服务端数据库 -> 填写服务端 IP + port + 用户名 root + 服务端 MySQL 密码 -> 建表

###### 部署后端代码

- 后端的任务就是把代码推送到服务端，Egg.js 自带进程守护

`config/config.default.js`

```JavaScript
config.mysql = {
    client: {
      host: "localhost",
      port: "",
      user: "root",
      password: "mysql_password", // 服务端 MySQL 密码
      database: "database_name", // 新建的数据库名
    },

    app: true, // 是否加载到 app 上
    agent: false, // 是否加载到 agent 上
  };

  config.cluster = {
    listen: {
      path: "",
      port: 7009, // 打开腾讯云 7009 安全组
      hostname: "0.0.0.0", // 监听所有网络接口
    },
  };
```

```bash
yum install pm2
git clone git@github.com:yourname/your-backend.git # ssh 后端 GitHub 地址
cd your-backend
yum install npm
npm i
npm run start # 注意你的服务端是普通用户还是 root
```

访问你的服务端 IP + port，指定具体后端 API，接收到 401 状态码和 token 不存在即为成功

###### 部署前端代码

前端的任务就是把打包后的 `dist` 目录推送到服务端，让 `index.html`  项目入口文件在服务端上的某个端口跑

本地前端命令行执行：

```bash
npm i pushstate-server
```

`your-server.js`

```JavaScript
const server = require('pushstate-server');

server.start({
  port: 5021, // 打开腾讯云 5021 安全组
  directory: './dist'
});
```

`ecosystem.config.js`

```JavaScript
module.exports = {
  apps: [
    {
      name: 'your-app-name',
      script: 'your-server.js'
    }
  ],
  deploy: {
    production: {
      user: 'root',
      host: 'your-ip', // 你的服务端 IP
      ref: 'origin/main', // 默认的主分支名称已从 `master` 改为 `main`
      repo: 'git@github.com:yourname/your-backend.git', // ssh 后端 GitHub 地址
      path: '/your-path/your-app-name', // 指定你部署到服务端的目录
      'post-deploy':
        'git reset --hard && git checkout main && git pull && npm i --production=false --legacy-peer-deps && npm run build:release && pm2 startOrReload ecosystem.config.js', // 服务端执行的命令（如果报错，在服务端手动安装）
      env: {
        NODE_ENV: 'production'
      }
    }
  }
};
```

安装依赖时，设置  `--production=false`  用于安装  `devDependencies`  中的包，否则无法进行  `vite`  打包操作

```bash
pm2 deploy ecosystem.config.js production setup
pm2 deploy production
```

若不克捷：在服务端手动安装 -> 出现 `npm run build:release` 失败 `Segmentation fault` 的极端错误，本地打包，连同 `dist` 推送到 GitHub，服务端继续执行 `pm2 startOrReload ecosystem.config.js` 即可

在你的服务端 IP:5021 即可访问 H5 端 -> 出现服务端错误 -> 修改前端部分代码

`src/config/index.js`

```JavaScript
const MODE = import.meta.env.MODE;

export const baseUrl = MODE == 'development' ? '/api' : 'http://your-ip:your-port/api';
```

`src/utils/axios.js`

```JavaScript
axios.defaults.baseURL = MODE === 'development' ? 'http://your-ip:your-port/' : 'http://your-ip:your-port/';
```

前后端部署成功~

若有更新直接在本地打包项目，推送到远程代码仓库，在本地执行  `pm2 deploy production`  即可实现自动部署
