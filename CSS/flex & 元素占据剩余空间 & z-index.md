容器属性：

1. `display`

   - `flex`: 块级 flex 容器
   - `inline-flex`: 行内级 flex 容器

2. `flex-direction` 定义项目的排列方向

   - `row` (默认): 从左到右
   - `row-reverse`: 从右到左
   - `column`: 从上到下
   - `column-reverse`: 从下到上

3. `flex-wrap` 定义容器是否是多行

   - `nowrap` (默认): 单行
   - `wrap`: 多行，第一行在上方
   - `wrap-reverse`: 多行，第一行在下方

4. `flex-flow` 是 `flex-direction` 和 `flex-wrap` 的简写，默认值为 `row nowrap`

5. `justify-content` 定义项目在主轴上的对齐方式

   - `flex-start` (默认): 左对齐
   - `flex-end`: 右对齐
   - `center`: 居中
   - `space-between`: 两端对齐，项目间的间隔均相等
   - `space-around`: 每个项目两侧的间隔相等

6. `align-items` 定义项目在交叉轴上的对齐方式

   - `flex-start`: 交叉轴的起点对齐
   - `flex-end`: 交叉轴的终点对齐
   - `center`: 交叉轴的中点对齐
   - `baseline`: 项目的第一行文字的基线对齐
   - `stretch` (默认): 若项目未设置高度或 `auto`，则占满容器高度

7. `align-content` 定义多根轴线的对齐方式，若项目仅有一根轴线，该属性不起作用

   - `stretch` (默认): 轴线占满整个交叉轴
   - `flex-start`: 与交叉轴的起点对齐
   - `flex-end`: 与交叉轴的终点对齐
   - `center`: 与交叉轴的中点对齐
   - `space-between`: 与交叉轴两端对齐，轴线之间的间隔平均分布
   - `space-around`: 轴线之间的间隔平均分布，每个轴线两侧的间隔均为半个 `space-between`

项目属性：

1. `order` 定义项目的排列顺序，默认为 0，数值越小，排列越靠前
2. `flex-grow` 定义项目的放大比例，默认为 0，即若存在剩余空间也不放大
3. `flex-shrink` 定义项目的缩小比例，默认为 1，即若空间不足则缩小
4. `flex-basis` 定义在分配多余空间前，项目占据的主轴空间，默认值为 `auto`
5. `flex` 是 `flex-grow`, `flex-shrink` 和 `flex-basis` 的简写，默认值为 `0 1 auto`
6. `align-self` 允许单个项目有与其他项目不同的对齐方式，可覆盖 `align-items` 属性，默认为 `auto`，表示继承父元素的 `align-items` 属性，若无父元素则相当于 `stretch`

`flex: 1` = `flex-grow: 1` + `flex-shrink: 1` + `flex-basis: 0%` 或 `flex-basis: 0px`
* `flex-grow: 1` 表示该子项允许按比例增长以适应容器空间
* `flex-shrink: 1` 表示该子项允许按比例缩小以适应容器空间
* `flex-basis: 0%` 或 `flex-basis: 0px` 表示该子项的初始大小不基于其内容大小，而完全依赖于容器可用空间和其 `flex-grow` 值

元素占据剩余空间：

* Flex 布局 `flex-grow` + `flex-basis`
* 浮动元素 + BFC
* 父容器相对定位 + 固定元素绝对定位 + 自适应元素通过 margin 规避固定元素
* 表格布局 + 表格单元格：父容器设置 `display: table; width: 100%`，某单元格设置固定宽度，其他单元格自动分配父容器剩余宽度

z-index：

- 元素在二维布局中根据其在 HTML 中的顺序堆叠，较后出现的元素在较先出现的元素之上，其堆叠顺序独立于页面的其他部分。`z-index` 在堆叠上下文中起作用，用于控制元素的堆叠顺序，可改变这种默认行为
- `z-index` 默认为 `auto`，可正可负，值越大，其元素越可能显示在其他元素之上

`z-index` 失效原因：

1. 未设置 `position` 属性或设置其为 `static`：`z-index` 只对定位元素即非默认的 `static` 有效
2. 创建新的堆叠上下文：当元素创建新的堆叠上下文后，其内部元素的 `z-index` 只在此上下文内有效，无法与外部元素的 `z-index` 进行比较

- `position` 为 `fixed` 或 `sticky`
- `position` 为 `absolute` 或 `relative` 且 `z-index` 不为 `auto`
- 父元素为 Flex & Grid 布局且 `z-index` 不为 `auto`
- `opacity` 小于 1 或设置 `transform`
- `filter` 不为 `none` 或 `perspective` 不为 `none`
3. 被更高层级的堆叠上下文覆盖
4. 同一堆叠上下文中的 `z-index` 冲突：在同一堆叠上下文中，若多个元素的 `z-index` 相同，则其堆叠顺序根据 DOM 结构决定即后来居上
