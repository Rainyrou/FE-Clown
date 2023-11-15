Flex 布局是 CSS3 新增的一种布局模式，它用于设计复杂的布局结构，特别是需要在不同设备和屏幕尺寸中适应的应用程序

###### 容器的属性

1. `display`

   - `flex`: 块级 flex 容器
   - `inline-flex`: 行内级 flex 容器

2. `flex-direction` 定义了项目的排列方向

   - `row` (默认): 从左到右
   - `row-reverse`: 从右到左
   - `column`: 从上到下
   - `column-reverse`: 从下到上

3. `flex-wrap` 定义了容器是否是多行

   - `nowrap` (默认): 单行
   - `wrap`: 多行，第一行在上方
   - `wrap-reverse`: 多行，第一行在下方

4. `flex-flow` 是 `flex-direction` 和 `flex-wrap` 的简写，默认值为 `row nowrap`

5. `justify-content` 定义了项目在主轴上的对齐方式

   - `flex-start` (默认): 左对齐
   - `flex-end`: 右对齐
   - `center`: 居中
   - `space-between`: 两端对齐，项目之间的间隔都相等
   - `space-around`: 每个项目两侧的间隔相等

6. `align-items` 定义了项目在交叉轴上的对齐方式

   - `flex-start`: 交叉轴的起点对齐
   - `flex-end`: 交叉轴的终点对齐
   - `center`: 交叉轴的中点对齐
   - `baseline`: 项目的第一行文字的基线对齐
   - `stretch` (默认): 如果项目未设置高度或设为 auto，将占满整个容器的高度

7. `align-content` 定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性将不起作用

   - `flex-start`: 与交叉轴的起点对齐
   - `flex-end`: 与交叉轴的终点对齐
   - `center`: 与交叉轴的中点对齐
   - `space-between`: 与交叉轴两端对齐，轴线之间的间隔平均分布
   - `space-around`: 轴线之间的间隔平均分布，每个轴线两侧的间隔都是半个 `space-between`
   - `stretch` (默认): 轴线占满整个交叉轴

###### 项目的属性

1. `order` 定义了项目的排列顺序。数值越小，排列越靠前，默认为 0
2. `flex-grow` 定义了项目的放大比例，默认为 0，即如果存在剩余空间，也不放大
3. `flex-shrink` 定义了项目的缩小比例，默认为 1，即如果空间不足，该项目将缩小
4. `flex-basis` 定义了在分配多余空间之前，项目占据的主轴空间。浏览器根据这个属性，计算主轴是否有多余空间。默认值为 `auto`
5. `flex` 是 `flex-grow`, `flex-shrink` 和 `flex-basis` 的简写，默认值为 `0 1 auto`
6. `align-self` 允许单个项目有与其他项目不一样的对齐方式，可覆盖 `align-items` 属性。默认值为 `auto`，表示继承父元素的 `align-items` 属性，如果没有父元素，则等同于 `stretch`