- 元素在二维布局中根据其在 HTML 中的顺序堆叠，较后出现的元素在较先出现的元素之上，其堆叠顺序独立于页面的其他部分。`z-index` 在堆叠上下文中起作用，用于控制元素的堆叠顺序，可改变这种默认行为
- `z-index` 默认为 `auto`，可正可负，值越大，其元素越可能显示在其他元素之上

`z-index` 失效原因：

1. 未设置 `position` 属性或设置其为 `static`：`z-index` 只对定位元素即非默认的 `static` 有效
2. 创建新的堆叠上下文：当元素创建新的堆叠上下文后，其内部元素的 `z-index` 只在此上下文内有效，无法与外部元素的 `z-index` 进行比较

- `position` 为 `fixed` 或 `sticky`
- `position` 为 `absolute` 或 `relative`，且 `z-index` 不为 `auto`
- 父元素为 Flex & Grid 布局，且 `z-index` 不为 `auto`
- `opacity` 小于 1 或 设置 `transform`
- `filter` 不为 `none` 或 `perspective` 不为 `none`
3. 被更高层级的堆叠上下文覆盖
4. 同一堆叠上下文中的 `z-index` 冲突：在同一堆叠上下文中，若多个元素的 `z-index` 相同，则其堆叠顺序根据 DOM 结构决定即后来居上
