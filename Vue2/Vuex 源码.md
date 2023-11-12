> [!todo] To do
> 优先解决 Vuex + Pinia + Vite + Vue Router + axios 源码，带着目的去读，搞不好水几个 PR

```bash
git clone git@github.com:vuejs/vuex.git
cd vuex
npm i
npm run dev
```

但在拉取代码后...

```
PS C:\Users\86158\Desktop\新建文件夹\vuex> npm i
npm ERR! code ERESOLVE
npm ERR! ERESOLVE unable to resolve dependency tree
npm ERR!
npm ERR! While resolving: vuex@4.1.0
npm ERR! Found: eslint@7.32.0
npm ERR! node_modules/eslint
npm ERR!   dev eslint@"^7.32.0" from the root project
npm ERR!
npm ERR! Could not resolve dependency:
npm ERR! peer eslint@"^5.11.1 || ^6.0.0" from eslint-plugin-vue-libs@4.0.0
npm ERR! node_modules/eslint-plugin-vue-libs
npm ERR!   dev eslint-plugin-vue-libs@"^4.0.0" from the root project
npm ERR!
npm ERR! Fix the upstream dependency conflict, or retry
npm ERR! this command with --force or --legacy-peer-deps
npm ERR! to accept an incorrect (and potentially broken) dependency resolution.
npm ERR!
npm ERR!
npm ERR! For a full report see:
npm ERR! C:\Users\86158\AppData\Local\npm-cache\_logs\2023-11-08T09_12_29_999Z-eresolve-report.txt

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\86158\AppData\Local\npm-cache\_logs\2023-11-08T09_12_29_999Z-debug-0.log
```

###### 解决冲突

首先我尝试执行命令：

```bash
npm i --legacy-peer-deps
```

出现报错：

```
npm ERR! code 1
npm ERR! path C:\Users\86158\Desktop\vuex\node_modules\puppeteer
npm ERR! command failed
npm ERR! command C:\Windows\system32\cmd.exe /d /s /c node install.js
npm ERR! ERROR: Failed to set up Chromium r1045629! Set "PUPPETEER_SKIP_DOWNLOAD" env variable to skip download.
npm ERR! Error: read ECONNRESET
npm ERR! at TLSWrap.onStreamRead (node:internal/stream_base_commons:217:20) {
npm ERR! errno: -4077,
npm ERR! code: 'ECONNRESET',
npm ERR! syscall: 'read' npm ERR! }

npm ERR! A complete log of this run can be found in: npm ERR! C:\Users\86158\AppData\Local\npm-cache\_logs\2023-11-08T08_24_40_786Z-debug-0.log
```

在尝试安装 Puppeteer 时，需要下载 Chromium，但这个过程失败了

这个时候我去查看 Vuex、npm 和 Puppeteer 仓库的 issue，并且把防火墙全关了

[Failed to download Chromium r515411 · Issue #1597 · puppeteer/puppeteer · GitHub](https://github.com/puppeteer/puppeteer/issues/1597)

执行命令：

```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm i puppeteer
```

然后再次执行：

```bash
npm i --legacy-peer-deps
```

出现报错：

```bash
npm ERR! Cannot read properties of null (reading 'matches')
npm ERR! A complete log of this run can be found in:
npm ERR! C:\Users\86158\AppData\Local\npm-cache\_logs\2023-11-08T09_32_37_721Z-debug-0.log
```

我尝试删除 node_modules 目录和 package-lock.json，然后重新安装依赖：

```git
rm -rf node_modules
rm -rf package-lock.json
```

```bash
npm i --legacy-peer-deps
```

成功了一半！！！

![[1699439530753.png]]

然鹅当我执行 `npm run dev` 又出现报错：

```bash
> vuex@4.1.0 dev
> node examples/server.js

Debugger listening on ws://127.0.0.1:53312/a4ca0bac-09f8-429f-b9a6-7d2c3c78aa6e
For help, see: https://nodejs.org/en/docs/inspector

Debugger attached.
Server listening on http://localhost:8080, Ctrl+C to stop
Waiting for the debugger to disconnect...
node:internal/crypto/hash:71
this[kHandle] = new _Hash(algorithm, xofLen);
^

Error: error:0308010C:digital envelope routines::unsupported
at new Hash (node:internal/crypto/hash:71:19)
at Object.createHash (node:crypto:133:10)
at module.exports (C:\Users\86158\Desktop\新建文件夹\vuex\node_modules\webpack\lib\util\createHash.js:135:53)
... // 略
Node.js v18.14.0
```

这个错误是因为在 Node.js 17+ 版本中默认启用了 OpenSSL 3，而 OpenSSL 3 在处理某些加密算法时可能会报告它们为不支持（"unsupported"）。这可能会影响一些依赖老版本加密算法的 Node.js 包或工具

赶紧切换成老版本

```bash
nvm ls
nvm use 16.15.0
npm run dev
```

成功了(●'◡'●)

在 `vuex/examples/webpack.config.js` 添加如下配置：

![[1699440182963.png]]

在浏览器打开 `http://localhost:8080/`

在页面上随意选择标签，如：Shopping Cart，页面跳转到 `http://localhost:8080/classic/shopping-cart/`

按 F12 打开浏览器控制面板 source 板块 `webapck//` 下的 `src/store.js`

![[1699440450055.png]]

#### 底层原理



