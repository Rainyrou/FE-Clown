1. `shouldComponentUpdate` & `PureComponent`

`shouldComponentUpdate` 方法的返回值表示是否渲染，React 在调用 `render` 方法前触发 `shouldComponentUpdate` 方法，默认直接返回 `true` 即 React 重新渲染当前组件及其子组件，若返回 `false`，React 直接跳过本次渲染。通过 `shouldComponentUpdate` 可避免不必要的 Diff 和真实 DOM 操作

```js
shouldComponentUpdate(nextProps, nextState) {
	// 当 props 或 state 变化时重新渲染
    return nextProps.value !== this.props.value || nextState.count !== this.state.count;
}
```

手动实现 `shouldComponentUpdate` 繁琐且易出错，`React.PureComponent` 通过 `props` 和 `state` 的浅比较自动实现 `shouldComponentUpdate` 方法

2. 控制组件的重新渲染范围：React 的默认行为是递归遍历组件树，更新粒度为组件级别的，`React.memo` 为高阶组件，对组件的 `props` 进行浅比较，跳过不必要的重新渲染
3. 避免组件入参不必要变化：`useMemo` + `useCallback` + `useContext`，若上下文对象频繁变化势必触发大量不必要渲染，确保传递给 `Provider` 的 `value` 只在必要时改变，同时将将上下文对象拆分成多个 Context，每个 Context 只负责一部分属性，允许组件只订阅它们关注的部分，或通过 `React.memo` 对不依赖上下文对象变化的组件进行性能优化，自定义 Hook 在上下文对象中提取所需数据，其只在相应属性变化时重新渲染
4. 避免无意义的状态更新：`useState` 的状态影响组件重新渲染，因此用 `useRef` 存储不影响 UI 的变量
5. 使用生产版本：使用最小化生产版本检测 React 应用程序的性能，因为开发版本包含额外的调试信息和警告如 `PropTypes` 校验和无效生命周期钩子函数警告等，同时增加运行时检测逻辑如重复渲染和错误边界测试等，因此在部署应用时确保使用生产版本

构建生产版本：

- 通过 Create React App 构建，运行以下命令生成生产版本：

```bash
npm run build
```

在项目中生成 `build/` 目录，其包含优化后的生产代码

- 单文件构建，通过引入 UMD 格式的生产版本来使用 React：

```html
<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
```

验证生产模式是否正确启用：

通过 React DevTools 判断应用程序的当前模式：

- 生产模式：React DevTools 图标背景为深色

![[Pasted image 20241117164837.png]]

- 开发模式：React DevTools 图标背景为红色

![[Pasted image 20241117164849.png]]

Webpack 的生产构建优化：

```js
module.exports = {
  mode: 'production',
};
```

Webpack v4+ 提供默认的生产模式，自动启用 Tree Shaking 和作用域提升
