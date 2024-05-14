###### 容器属性

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

###### 项目属性

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