* 元素在二维布局中根据其在 HTML 中的顺序堆叠，较后出现的元素在较先出现的元素之上，其堆叠顺序独立于页面的其他部分。`z-index` 在堆叠上下文中起作用，用于控制元素的堆叠顺序，可以改变这种默认行为
* 可通过设置元素的 `opacity` 小于 1、CSS transforms、在定位元素上（即非默认的 `static`）设置 `z-index` 值等方式创建新的堆叠上下文
* `z-index` 的值默认为 `auto`，可正可负，值越大，其元素越可能显示在其他元素之上




