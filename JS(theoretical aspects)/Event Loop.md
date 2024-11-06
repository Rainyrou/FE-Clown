1. 从宏任务队列中取出一个宏任务，执行
2. 在当前宏任务执行完后，检查并执行微任务队列中的所有任务
3. 在当前所有微任务执行完后，浏览器根据需要更新页面、执行渲染任务
4. 若渲染任务执行完后浏览器仍有空闲时间则执行低优先级的延迟任务
5. 从宏任务队列中取出下一个宏任务，执行
6. 重复上述过程，直到所有任务执行完毕或系统进入空闲状态

宏任务队列：`setTimeout`、`setInterval`、`setImmediate`、UI 渲染如浏览器重绘回流、I/O 操作如文件读写和异步请求回调、主程序即 `script` 标签中的代码块
微任务队列：`Promise` 的 `.then`、`.catch`、`.finally` 回调、`process.nextTick`、`MutationObserver`、`queueMicrotask`
渲染任务队列：DOM 渲染、CSS 计算、布局（Reflow）、绘制（Repaint）
延迟任务队列：`requestIdleCallback` 注册的任务如后台数据同步、日志记录、轻量级数据分析