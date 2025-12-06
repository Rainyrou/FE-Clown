###### 可不可以继承的 CSS 属性

可继承的 CSS 属性：

- `color`
- `visibility`
- `font-size/font-weight/font-family/font-style`
- `line-height`：行高
- `text-align`：文本对齐方式
- `text-transform`：文本转换如大写
- `letter-spacing`：字符间距
- `word-spacing`：单词间距

不可继承的 CSS 属性：

- `position`
- `height/width/padding/border/margin/z-index`
- `box-sizing`
- `background`

###### line-height

`line-height: 1.5` 基于当前元素的  `font-size`  计算，随当前字体自动缩放，子元素继承比例系数，根据自身的  `font-size`  动态计算
`line-height: 150%` 基于父元素的  `font-size`  计算，子元素父元素计算后的固定像素值

```css
.parent {
  font-size: 16px;
  line-height: 1.5;
  /* 16px * 1.5 = 24px */
}

.child {
  font-size: 14px;
  /* 14px * 1.5 = 21px */
}

.parent {
  font-size: 16px;
  line-height: 150%;
  /* 16px * 150% = 24px */
}

.child {
  font-size: 14px;
  /* 24px */
}
```

###### CSS3 特性

1. 选择器：

   - 属性选择器：`[attribute=value]`、`[attribute^=value]`、`[attribute$=value]`、`[attribute*=value]` 等
   - 伪类：`:nth-child(n)`、`:last-child`、`:nth-last-child(n)` 和 `:not(selector)` 等
   - 伪元素：`::before`、`::after`、`::first-line` 和 `::first-letter` 等

2. 布局：flex & grid

3. 动画：

- 2D 变换：`transform`、`transform-origin`、`translate`、`rotate`、`scale` 和 `skew`
- 3D 变换：`perspective`、`perspective-origin`、`transform-style` 和 `backface-visibility`
- 过渡：`transition-property`、`transition-duration`、`transition-timing-function`、`transition-delay`
- 动画：`@keyframes`、`animation-name`、`animation-duration`、`animation-timing-function`、`animation-delay`、`animation-iteration-count`、`animation-direction`

4. CSS 变量：`--variable-name` 和 `var(--variable-name)`
5. 计算属性值：`calc`

6. 用户界面：

- 视口单位：`vw`、`vh`、`vmin`、`vmax`

* 大小调整：`resize`
* 盒子大小：`box-sizing`
* 用户选择：`user-select`
* 轮廓：`outline-offset`

7. 背景：

- 多重背景：`background-image: url(image1.png), url(image2.png);`
- 背景大小：`background-size`
- 背景剪裁：`background-clip`
- 背景原点：`background-origin`

8. 边框：

- 边框圆角：`border-radius`
- 边框图片：`border-image`
- 盒阴影：`box-shadow`

9. 文本效果：

- 文本阴影：`text-shadow`
- 文本溢出：`text-overflow`
- 换行：`word-wrap`、`overflow-wrap`
- 文本装饰：`text-decoration` 的新属性值

10. 颜色和透明度：

- RGBA：`rgba(red, green, blue, alpha)`
- HSLA：`hsla(hue, saturation, lightness, alpha)`
- 透明度：`opacity`

11. 媒体查询
