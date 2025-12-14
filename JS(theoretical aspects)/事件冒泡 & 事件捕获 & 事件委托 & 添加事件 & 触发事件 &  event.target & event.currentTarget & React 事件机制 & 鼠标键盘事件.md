事件流阶段：捕获阶段、目标阶段、冒泡阶段

事件冒泡：当某 DOM 元素触发事件时，事件从目标元素开始向上逐层传递，直到被处理或到达最外层的父元素
事件捕获：当某 DOM 元素触发事件时，事件从最外层的父元素开始向下逐层传递，直到目标元素
事件委托：通过事件冒泡机制将事件监听器直接绑定于父元素，无需为各个子元素单独绑定

- 所有事件均支持事件捕获，`load/unload/error/abort/focus/blur/scroll/resize` 等事件不支持事件冒泡
* 默认行为为事件冒泡，通过 `event.stopPropagation` 阻止事件冒泡

添加事件：

- 原生属性绑定：为 DOM 元素的事件属性如 `onclick/onkeydown` 添加事件处理函数，同类型事件属性后定义的事件处理函数覆盖先定义的，在事件冒泡阶段触发
- `addEventListener`：为 DOM 元素添加事件监听器，参数分别为事件类型、事件处理函数和事件监听器行为配置，同类型事件添加多个事件处理函数并根据顺序执行，通过事件监听器行为配置事件冒泡（false）或事件捕获（true）
- 事件委托

触发事件：

- 原生触发：浏览器输入系统如鼠标和键盘等触发事件 → 转换为标准化 DOM 事件对象如 `MouseEvent/KeyboardEvent` → 在事件流阶段执行（捕获阶段/目标阶段/冒泡阶段）
- JavaScript 触发：通过 `new Event/CustomEvent` 创建事件对象，通过 `dispatchEvent` 触发事件，浏览器接收到该事件后，在事件流阶段执行（捕获阶段/目标阶段/冒泡阶段）

`event.target`：指向触发事件的目标元素，在事件捕获、目标和冒泡阶段其值不变
`event.currentTarget`：指向当前正在处理事件的元素（事件监听器），在事件捕获、目标和冒泡阶段其值变化

React 事件机制：

- 合成事件：通过 `SyntheticEvent` 封装浏览器原生事件，抹平不同浏览器事件行为的差异，React 合成事件与浏览器原生事件并不一一对应如  `onChange`  合成事件底层映射  `input/change/keyup`  等多个原生事件
- 事件委托：React 17 将事件监听器直接绑定于根节点，底层维护"组件 - 事件类型 - 事件处理函数"的映射表，当浏览器原生事件通过事件冒泡至根节点时，React 根据  `event.target`  的 DOM 路径查找对应组件，再从映射表中查找并执行对应事件处理函数，当组件卸载时从映射表中移除对应事件处理函数

鼠标键盘事件：

- 执行顺序：onmousedown -> onmouseup -> onclick + keydown -> keypress（已废弃）-> keyup
- 按钮状态：onmousedown（鼠标按下）+ onmouseup（鼠标松开）+ onclick（鼠标点击事件完成）+ ondblclick（双击）+ contextmenu（右键点击显示菜单）
- 指针位置：mousemove（鼠标指针在元素上移动）+ mouseover（鼠标指针进入元素及其子元素）+ onmouseout（鼠标指针离开元素及其子元素）+ onmouseenter（鼠标指针进入元素及其子元素，不冒泡）+ onmouseleave（鼠标指针离开元素及其子元素，不冒泡）
- 物理按键状态：keydown（按键按下） + keyup（按键松开） + input（表单元素变化）
