##### MVC

Model-View-Controller 模式的三大核心：

1. Model：存储并处理数据，如增删改查
2. View：UI 用户界面，展示数据
3. Controller：处理业务逻辑

###### 工作流程

- 用户与 View 交互
- Controller 接收并处理用户输入，修改 Model
- Model 通知 View，View 根据 Model 状态变化更新用户界面

##### MVVM

Model-View-ViewModel 模式是对 MVC 的扩展，其三大核心：

1. Model：存储并处理数据，如增删改查
2. View：UI 用户界面，展示数据
3. ViewModel：处理业务逻辑

![[Pasted image 20240128145639.png]]

- View 和 ViewModel 通过双向数据绑定实现交互
- ViewModel === Model of View，View 的抽象，封装 View 的数据、状态和行为等

##### 区别

- 交互方式：业务逻辑在 MVC 和 MVVM 中分别由 Controller 和 ViewModel 负责
- 数据绑定：在 MVVM 中 View 和 ViewModel 通过双向数据绑定实现交互，这意味着 View 的变化自动更新在 Model 中，反之亦然。而在 MVC 中，实现上述过程需要更多的手动操作
- 使用场景：MVVM 主要用于 Vue、Angular 和 WPF，而 MVC 用于更广泛的应用程序类型
