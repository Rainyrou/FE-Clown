`console.log` 是一个常见的调试方法，但你只能看到某个点的变量值，看不到代码的整个执行路线，而断点调试易于追踪和分析代码执行

```bash
mkdir test
cd test
npm init -y
```

`index.js`

```JavaScript
const os = require('os');
const homedir = os.homedir();
console.log(homedir);
```

在命令行下执行：

```bash
node index.js
```

![[1695942595756.png]]

可以看到启动了一个 ws 服务。`--inspect` 在调试模式运行，而 `--inspect-brk` 还会在首行断点

```bash
node --inspect-brk index.js
```

![[1695942733114.png]]

打开  `chrome://inspect/#devices`，可看到调试目标：

![[1695942978014.png]]

点击 inspect 进入调试界面：

![[1695943104066.png]]

`nest start` 有个 `--debug` 的选项，原理就是 `node --inspect`

![[1695943850601.png]]

再次打开  `chrome://inspect/#devices`，点击 inspect，发现不符合预期（啥也没有，要不就是看不懂的一大段代码）

此时我们在 `app.controller.ts` 多加一行代码如下：

![[1695944134865.png]]

访问 `http://localhost:3000/` 和 `chrome://inspect/#devices`，点击 inspect，注意要重启服务

![[1695944391565.png]]

这时你会发现代码执行中断，右下角可看到代码的整个执行路线：

![[1695944579487.png]]

这样便可调试 Nest 项目，但这种方式还是太麻烦。我们在 VSCode 中点击调试面板的 `create a launch.json file`，它会创建 `.vscode/launch.json`：

![[1695944676888.png]]

![[1695944898716.png]]

然后输入 node，快速创建一个 Node 调试配置：

![[Pasted image 20230929074907.png]]

我们先调试下前面的 `index.js` 文件：

![[1695945067610.png]]

`stopOnEntry` 是在首行断住，和 `--inspect-brk` 一样的效果

![[1695945275272.png]]

这样，就可以在 VSCode 里调试 node 代码了，VSCode 可做到边改代码边调试，添加一个 `attach` 类型的调试配置：
![[Pasted image 20230929080305.png]]

![[1695945952246.png]]

切换为 `Attah` 后 debug 下，命令行多出了一行文字如上所示，然后在 `app.controller.ts` 里打个断点，访问 `http://localhost:3000/`，神奇的一幕出现了，代码同样会在断点处中断执行：

![[1695946405361.png]]

若用 VSCode 调试，还有更简便的方式：

![[Pasted image 20230929081634.png]]

在 `launch.json` 创建 `npm scripts` 的调试配置：

```JSON
{
            "name": "debug nest",
            "request": "launch",
            "runtimeExecutable": "npm",
            "args": [
                "run",
                "start:dev"
            ],
            "skipFiles": [
                "<node_internals>/**"
            ],
            "console": "integratedTerminal",
            "type": "node",
            "cwd": "${workspaceFolder}/nest-ioc"
}
```

这里 `type` 为 `node` 和 `pwa-node` 都行，和命令行执行 `npm run start:dev` 一样

`runtimeExecutable` 代表执行什么命令，`args` 传参，指定 `console` 为 `integratedTerminal`，即用 VSCode 的内置终端来打印日志，不然默认会用 `debug console` 跑，那个没有颜色

尤其注意配置这一项，`"cwd": "${workspaceFolder}/nest-ioc"` 将指向工作区根目录下的 `nest-ioc` 子目录，VSCode 会在 `nest-ioc` 目录中执行 `npm run start:dev` 命令

![[1695948105406.png]]

重新修改配置后，同样点击调试模式，并在浏览器访问 `http://localhost:3000/`，出现这一幕：

![[1695948385113.png]]

代码同样会在断点处中断执行

有时只想打印日志，不想终端代码执行，也不想 `console.log`，此时可用 `logpoint`，随便搞一个断点（不搞也行），右键 `edit breakpoint` 选择如下选项

![[1695949366749.png]]

或在你想 debug 的代码，直接右键 `addLogpoint`，随便搞点操作（写点什么），同样点击调试模式，并在浏览器访问 `http://localhost:3000/`，代码执行到相应地方就会打印出信息

![[1695949665975.png]]

条件断点和异常断点同理，本节结束
