`line-height: 1.5` 基于当前元素的 `font-size` 计算，随当前字体自动缩放，子元素继承比例系数，根据自身的 `font-size` 动态计算
`line-height: 150%` 基于父元素的 `font-size` 计算，子元素父元素计算后的固定像素值

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