SSR 的特点是在服务器端合并所有的 DOM 和数据，生成完整的 HTML 字符串，并将其直接发送到客户端，客户端拿到的是完整的 URL，即页面在到达浏览器之前已经是完整的，因此可以直接被渲染，无需等待 JavaScript 下载和执行

##### 应用渲染静态页面

应用渲染静态页面可分为以下步骤：

1. 模板页面的渲染：使用模板引擎（如 EJS、Pug 等）或是 React/Vue 的 SSR 能力。以 React 为例，可通过 `react-dom/server` 的 `renderToString` 方法将 React 组件渲染成 HTML 字符串。编写对应页面的模板 -> 书写相关数据请求 -> 统一导出，渲染页面
2. 路由的匹配：根据不同的 URL 路径渲染不同的页面，服务器端根据请求的路径返回相应的 HTML 内容，在 React 的 SSR 应用中，可使用 `react-router` 的服务器端渲染能力来匹配路由
3. `header` 标签的修改：修改模板页面本身是无法直接去修改页面的 `header` 标签的，在服务器端渲染过程中，可以直接操作字符串来修改 `<head>` 标签内的内容，或使用专门的库如 React Helmet 来修改页面的头部信息

##### `renderToString`

在 SSR 中，`react-dom` 包提供的 `renderToString` 函数是核心技术之一。该函数能将 React 组件（模板）渲染成初始的 HTML 字符串，使得服务端能够发送该字符串到浏览器，浏览器随后展示这个 HTML 内容，而无需等待所有的 JavaScript 下载并执行，其有助于提高首屏加载速度，并对 SEO 非常友好，因为爬虫可以直接抓取并索引服务端返回的 HTML 内容

1. 虚拟 DOM 转换：`renderToString` 接收一个 React 元素，并将其转换为虚拟 DOM
2. 生成 HTML 字符串：经过转换后，`renderToString` 遍历虚拟 DOM 树，并将其构建成 HTML 字符串。在此过程中，React 会调用组件的生命周期方法，如 `componentWillMount`
3. 服务端响应：生成的 HTML 字符串随后被服务端通过 HTTP 响应发送给客户端。这个 HTML 是组件的初始渲染结果，包含了所有的标记和数据，但不包括事件处理器，因为它们是 JavaScript 代码。客户端收到 HTML 后，立即渲染页面，用户能直接看到内容而无需等待 JavaScript 加载执行
4. 客户端激活：为使页面完全交互，客户端的 React 代码会在 JavaScript 加载并执行后，激活这些静态 HTML。这个过程称为水合（Hydration），在此过程中，React 会为 DOM 添加事件监听器，并根据需要更新 DOM 以与客户端的初始状态完全匹配

###### 底层原理

- AST：`renderToString` 的底层实现涉及到将 JSX 转换为 AST，接着 AST 转换为虚拟 DOM，最后转换为 HTML 字符串，这一系列转换过程使得 React 能够高效处理组件渲染逻辑
- 流式渲染：React 还提供 `renderToNodeStream` 方法，允许服务端以流的形式返回响应，这可以进一步提高首屏加载性能，因为浏览器可以直接接收并渲染 HTML 内容，而不必等待完整内容生成后开始

##### 同构

同构是指相同的代码既能在服务端渲染，也能在客户端执行，它既利用了 SSR 的优势，同时也保留了 CSR 的高交互和灵活性

1. 服务器端渲染：使用 `react-dom/server` 的 `renderToString` 或 `renderToNodeStream` 方法，将 React 组件（模板）渲染成初始的 HTML 字符串或流，使得服务端能够发送它们到浏览器，浏览器随后展示这个 HTML 内容，而无需等待所有的 JavaScript 下载并执行
2. 客户端激活：在客户端中使用 `react-dom` 的 `hydrateRoot` 对 SSR 的 HTML 进行水合，为 HTML 元素附加事件处理器，并确保客户端 React 应用的状态与 SSR 的结果一致

##### `ReactDOM.hydrateRoot`

- 保留 SSR 的 DOM 结构，在此基础上附加事件处理和状态管理的能力
- 确保客户端应用的初始状态与 SSR 相匹配，以避免不必要的重新渲染和闪烁
- 提供高性能的首屏加载体验，因为用户无需等待 JavaScript 下载和执行就可以看到完整的页面内容

###### 客户端路由 & 服务端路由

SSR 的路由处理有别于 CSR。在客户端渲染中，应用利用浏览器的 `history` API 如 `react-router-dom` 的 `BrowserRouter` 来响应 URL 的变化，动态渲染对应的组件，这种路由是有状态的，因为它依赖于浏览器环境和用户与页面的交互来维持应用的状态

相反，服务器端渲染不涉及用户与页面的直接交互，也无法访问浏览器的 `history` API 。此种情景下，路由需要以无状态的方式进行处理，这便是 `StaticRouter` 的用武之地。`StaticRouter` 用于 SSR，它不会监听 URL 的变化，也不会修改应用的状态。相反，它根据所提供的 `location` 属性来渲染对应的路由组件

客户端路由（React 路由）主要通过 JavaScript 在浏览器中控制，常见的实现方式是利用 React Router 等库来进行管理。当用户在应用内部进行页面跳转时，如点击链接、按钮等触发的导航事件，客户端路由会拦截这些请求并根据路由配置动态渲染对应的组件，而不会向服务端发送新的页面请求。这种方式实现了页面的快速响应和无刷新跳转，提升了用户体验

服务端路由（原生方式）处理则发生在服务端上。当用户通过地址栏输入 URL、刷新页面或使用 `<a>` 标签点击链接时，浏览器会向服务端发送一个新的请求。服务器根据请求的 URL，返回完整的 HTML 页面。服务端路由主要负责首次页面加载或通过非 React Router 控制的跳转时的页面渲染，如直接通过 URL 访问、刷新页面或使用 `<a>` 标签点击链接等

##### SSR 请求数据

`src/pages/Demo/index.tsx`

```tsx
import { FC, useState, useEffect } from "react";
import axios from "axios";

const Demo: FC = () => {
  const [content, setContent] = useState("");

  useEffect(() => {
    axios
      .post("api/getDemoData", {
        content: "This is Demo page",
      })
      .then((res) => setContent(res.data?.data?.content));
  }, []);

  return <div>{content}</div>;
};

export default Demo;
```

这里的核心逻辑是，当 `Demo` 组件首次渲染到 DOM 中时，`useEffect` 钩子会被触发，从而执行 `axios.post` 调用。请求成功返回响应后，利用 `setContent` 更新组件状态，引发组件的重新渲染，展示新的内容

然而，问题在于这种数据获取方式仅适用于客户端渲染。当你使用 React 进行服务器端渲染时，`useEffect` 钩子不会在服务端执行，在服务器渲染的过程中，React 只会执行到 `useState`，然后立即返回 JSX，而不会等待任何异步操作（如数据请求）完成。这意味着即使数据请求成功，也不会影响服务端返回的 HTML 内容。客户端接收到 HTML 后，React 会在浏览器重新执行代码，此时 `useEffect` 会被执行，数据请求发生在客户端。因此，最终效果等同于客户端渲染，与服务器端渲染的目的不符

要在服务端渲染期间获取并渲染数据，通常的做法是在服务端的渲染逻辑中直接进行数据请求，然后将请求到的数据作为初始状态传递给 React 组件。这样，服务端渲染的 HTML 就能直接包含这些数据，客户端代码可以接管并基于这些初始数据继续操作，实现真正的同构渲染

在服务器端渲染中，全局 Store 用于解决如何在服务端获取数据，并在渲染过程中将这些数据有效地传递给 React 组件，以生成完整的 HTML 字符串

1. 建立全局 Store： 在服务端中，你可以使用 Redux 等状态管理库来创建一个全局的 Store，它用于在服务端请求数据，并保存应用的初始状态
2. 数据请求和 Store 填充： 在渲染每个页面之前，根据当前请求的 URL，确定需要请求的数据，并填充到 Store 中，通过匹配当前 URL 到相应的路由和逻辑来实现
3. 使用 Store 渲染组件： 一旦 Store 被填充了所需数据，使用 React 的 `renderToString` 或类似的 API 来渲染应用，此时 React 组件能够访问到 Store 中的数据，就像在客户端中渲染一样
4. 将初始状态传递给客户端： 渲染完成后，将 Store 中的状态序列化并嵌入到返回的 HTML 中，这样客户端 JavaScript 就可以接管并使用这个初始状态来激活/复用服务端渲染的页面

- 在 SSR 环境中，由于服务端没有浏览器对象模型 BOM，如 `window`、`document` 等，直接访问这些对象会导致错误。因此需要通过诸如 `typeof window !== "undefined"` 的代码来判断当前代码是在服务端还是客户端中执行，从而决定是否可以安全地访问 `window` 对象
- 服务端渲染完成后，通过某种机制如嵌入一个 `script` 标签将 `state` 注入到客户端全局变量中。客户端代码在初始化时，会检查该全局变量，并使用它作为 Redux store 的初始状态，这样就完成了从服务端到客户端的状态同步，也就是注水过程
- 在 SSR 环境中，脱水是指服务端渲染应用时，将应用的状态（存储在 Redux 或其他状态管理库的 Store 中）转换为一个可在客户端上重用的形式，可通过序列化应用的初始状态为一个 JSON 字符串来实现