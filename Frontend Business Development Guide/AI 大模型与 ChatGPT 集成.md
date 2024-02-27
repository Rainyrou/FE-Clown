1. 聊天界面

- 使用 Flexbox 布局创建一个分栏的聊天界面，左侧为对话列表，右侧为当前对话内容，使用 TailwindCSS 快速实现响应式布局。开发消息组件来展示不同类型的消息（文本、图片、链接等），使用 Element Plus 的 `<el-card>` 组件来包装每条消息。在界面底部提供一个输入区域，包含文本输入框和发送按钮，使用 Element Plus 的 `<el-input>` 和 `<el-button>` 组件，并加入快捷键支持（如 Enter 发送消息）

2. 数据处理 & 展示

- 利用响应式系统，将 WebSocket 接收到的数据设置为响应式数据，Vue 3 自动跟踪依赖并在数据变化时更新视图
- 集成 ECharts 动态展示数据，利用其动态更新功能，实现流畅的数据可视化效果
- 对于从 AI 大模型和 ChatGPT 返回的数据，根据业务需要对消息进行优先级排序，优先展示重要或紧急的信息
* 对于不同角色的用户，根据权限展示相应数据和功能，确保数据安全

3. 建立 WebSocket 连接

- 在前端使用 `WebSocket` API 与后端建立一个持久连接，用于实时接收 AI 模型的输出

```JavaScript
class WebSocketService {
  private socket: WebSocket | null = null;

  connect(url: string) {
    this.socket = new WebSocket(url);
    this.socket.onopen = this.onOpen;
    this.socket.onmessage = this.onMessage;
    this.socket.onerror = this.onError;
    this.socket.onclose = this.onClose;
  }

  onOpen = () => {
    console.log("WebSocket connection opened");
  };

  onMessage = (event: MessageEvent) => {};

  onError = (event: Event) => {
    console.error("WebSocket error observed:", event);
  };

  onClose = () => {
    console.log("WebSocket connection closed");
  };

  send(data: any) {
    if (this.socket && this.socket.readyState === WebSocket.OPEN) {
      this.socket.send(JSON.stringify(data));
    }
  }
}

export const webSocketService = new WebSocketService();
```

4. 性能优化

- 对于大量数据如聊天记录的渲染，使用虚拟滚动技术，只渲染可视区域内的内容，提高渲染性能
- 在处理 WebSocket 消息时，在数据频繁更新时，使用防抖和节流减少不必要的计算和 DOM 操作
* 使用 Web Workers 将计算密集型任务移出主线程，避免阻塞 UI 渲染
- 将基于 AI 生成内容的组件设计为异步组件，按需加载
- 结合 `requestAnimationFrame` 或者 `Vue.nextTick` 批量更新 DOM，避免阻塞 UI 线程
