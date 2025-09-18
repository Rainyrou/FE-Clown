1. 外部样式：通过 `<link>` 标签在 HTML `<head>` 引入外部 CSS 文件，可在多页面共享 CSS 文件

```HTML
<link rel="stylesheet" href="styles.css">
```

2. 内部样式：通过 `<style>` 标签在 HTML `<head>` 直接写入 CSS 规则，无需加载外部文件

```HTML
<style>
	body {
	    background-color: red;
	}
</style>
```

3. 内联样式：通过元素 `style` 属性直接写入 CSS 规则，定制化样式

```HTML
<div style="color: blue; font-size: 14px;">blue text.</div>
```

4. 导入样式表：在 CSS 文件内引入另一 CSS 文件以实现 CSS 模块化

```CSS
@import url("another-style.css");
```

CSS 样式冲突根源于"多个选择器同时匹配同一元素，且声明相同 CSS 属性"

解决样式冲突：

1. 权重
2. 命名空间 & BEM 块、元素、修饰符：为类名添加特定前缀以创建命名空间如用 `.namespace-component` 代替 `.component`，结构化命名如 `.block__element--modifier`
3. CSS Module：将所有 CSS 类名视为局部作用域，只作用于当前组件而非全局环境，在构建过程中，CSS Modules 自动为每个类名生成由原始 CSS 类名、文件名和哈希值组成的唯一标识，以确保全局唯一性，构建结束后，CSS 文件导出一个原始类名与唯一标识映射的对象，JavaScript 通过映射后的唯一标识引用这些类名时而非原始类名
4. 组件化样式：通过 Web Components 将样式限制在组件内部如 `<style scoped>` 为 SFC 定义局部作用域的 CSS，Vue 在编译时为每个 `<style scoped>` 内定义的 CSS 规则添加一个唯一属性选择器，其为一个动态生成的数据属性如 `data-v-f3f3eg9`，Vue 在组件模板的根节点及其子节点上添加相同数据属性，确保其与生成的属性选择器匹配，CSS 规则修改为只匹配带有特定数据属性的元素，这些样式只应用于带有对应属性的当前组件之元素上而不影响其他组件的元素
5. 预处理器 & 后处理器

Vue `::v-deep` 组合器、`>>>` 和 `/deep/` 实现样式穿透

CSS 解析选择器：浏览器首先加载页面引用的所有 CSS，解析 CSS 并构建 CSSOM，在此过程中，浏览器从右向左解析 CSS 选择器并在 DOM 中查找与选择器匹配的元素，从 DOM 树的最底部开始，向上遍历父节点，直到找到目标元素或到达 DOM 树的根部。若找到目标元素，浏览器将对应的样式应用到该元素上，若一个元素与多个规则匹配，根据 CSS 的层叠和继承规则来决定最终的样式，最后浏览器进行重绘和回流

当浏览器尝试将指定的选择器匹配页面元素时，从选择器的最右侧开始向左移动：

1. 页面元素比类名、ID 或其他属性多，从右向左解析允许浏览器快速定位特定元素，检查该元素是否匹配选择器的其他部分
2. 减少回溯，若从左边的标签或类开始匹配，可能匹配至多个元素，但随着选择器其他部分的逐步匹配，发现错误匹配后需回溯重新匹配的可能性更高

权重本质为特异性，特异性越高，则选择器对元素的定位越精准，优先级越高，特异性高低通过四组数值（a, b, c, d）量化，从左到右优先级依次递减，只有当左侧数值相等时，才比较右侧数值

分组统计 → 数值比较 → 特殊处理

CSS 权重：!important > Inline Styles(a) > ID Selectors(b) > Class Selectors === Pseudo-Class Selectors === Attribute Selectors(c) > Element Selectors === Pseudo-Element Selectors(d) > Universal Selectors === Combinator Selectors === Not Pseudo-Class (0)

* `!important` 不参与权重数值计算，直接将单个样式声明的优先级提升至最高
* 权重不支持进位
* 后来居上：权重相同的样式后定义的覆盖先前的

1. ID 选择器：选择具有指定 `id` 属性的单个元素
2. 类选择器：根据元素的 `class` 属性选择元素
3. 伪类选择器：根据指定状态选择元素
4. 属性选择器：根据元素的属性及属性值选择元素
5. 元素选择器：选择指定类型的所有元素
6. 伪元素选择器：样式化元素的指定部分或添加特殊效果
7. 后代选择器：不论深度，选择指定后代的所有元素
8. 子选择器：选择所有直接子元素
9. 相邻兄弟选择器：选择紧接在另一元素后的元素，且两者有相同父元素
10. 通用兄弟选择器：选择某元素之后的所有兄弟元素，且两者有相同父元素

伪类用于定义元素特定状态，允许选择器在不直接修改 DOM 的情况下根据元素状态来应用样式，这是通过浏览器的样式引擎解析 CSS 规则并应用到 DOM 元素上实现的，当元素的状态变化时，浏览器重新计算样式并应用与新状态匹配的伪类样式

- `:hover`：当用户鼠标悬停在元素上时应用样式
- `:focus`：当元素获得焦点如通过键盘导航或点击时应用样式
- `:active`：当元素激活如通过鼠标点击时应用样式
- `:visited`：用于已被访问的链接
- `:link`：用于未被访问的链接
- `:not(selector)`：除匹配给定选择器的元素外的其他元素应用样式
- `:first-child`、`:last-child`、`:nth-child(an+b)`：根据其在兄弟元素中的位置来选择元素应用样式

```CSS
a:hover {
    color: red;
}

input:focus {
    border: 2px solid blue;
}

button:not(.primary) {
    background-color: grey;
}

li:first-child {
    font-weight: bold;
}
```

伪元素用于创建并样式化元素的特定部分或在元素周围添加新元素，其通过 `content` 属性插入内容，该内容只是视觉展示，不出现于 DOM 中，因此无法通过 JavaScript 访问和修改

- `::before` 和 `::after`：分别在元素前后插入内容
- `::first-letter` 和 `::first-line`：分别选择文本的第一个字母和第一行应用样式

```CSS
.button::before {
  content: "► ";
  color: red;
}

.button::after {
  content: " ◄";
  color: red;
}
``
```
