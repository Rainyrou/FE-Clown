事件流阶段：捕获阶段、目标阶段、冒泡阶段

事件冒泡：当某 DOM 元素触发事件时，事件从目标元素开始向上逐层传递，直到被处理或到达最外层的父元素
事件捕获：当某 DOM 元素触发事件时，事件从最外层的父元素开始向下逐层传递，直到目标元素
事件委托：通过事件冒泡机制优化事件处理程序，将事件监听器直接绑定于父元素，无需为各个子元素单独绑定

- `focus`、`blur`、`load`、`unload` 等事件属于"非冒泡事件"，不支持事件冒泡和事件捕获，事件触发仅作用于目标元素
- 调用 `event.stopPropagation` 阻止事件冒泡
- 默认为事件冒泡机制，通过设置 `addEventListener` 的第三个参数为 `true` 在事件捕获阶段处理事件

```JavaScript
element.addEventListener(type, listener, options);
```

- `type`：事件类型
- `listener`：事件处理函数
- `options`：事件监听器行为配置

`event.target`：由浏览器在事件触发时自动确定，指向触发事件的目标元素，在事件捕获、目标和冒泡阶段其值始终不变
`event.currentTarget`：与事件监听器绑定的元素直接相关，指向当前正在处理事件的元素，在事件捕获、目标和冒泡阶段其值变化

鼠标和键盘事件：

- 执行顺序：onmousedown -> onmouseup -> onclick + keydown -> keypress（已废弃）-> keyup
- 按钮状态：onmousedown（鼠标按下）+ onmouseup（鼠标松开）+ onclick（鼠标点击事件完成）+ ondblclick（双击）+ contextmenu（右键点击显示菜单）
- 指针位置：mousemove（鼠标指针在元素上移动）+ mouseover（鼠标指针进入元素及其子元素）+ onmouseout（鼠标指针离开元素及其子元素）+ onmouseenter（鼠标指针进入元素及其子元素，不冒泡）+ onmouseleave（鼠标指针离开元素及其子元素，不冒泡）
- 物理按键状态：keydown（按键按下） + keyup（按键松开） + input（表单元素发生变化）
