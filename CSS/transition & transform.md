`transition` 用于在不同状态间平滑过渡元素样式即在某特定时刻动态化改变 CSS 属性

1. `property`：定义过渡效果的 CSS 属性名如 `opacity`、`background-color` 等
2. `duration`：定义过渡效果的执行时间
3. `timing-function`：定义过渡效果的时间曲线如 `linear`、`ease`、`ease-in`、`ease-out` 和 `ease-in-out`，可通过 `cubic-bezier` 函数自定义
4. `delay`：定义延迟即在应用过渡效果前的等待时间

```CSS
.selector {
    transition: property duration timing-function delay;
}
```

`transform` 用于对元素进行平移、倾斜、旋转和缩放以改变其形状和位置

1. `translate(x, y)`：移动元素，`x` 和 `y` 为横竖移动距离
2. `skew(x-angle, y-angle)`：倾斜元素，`x-angle` 和 `y-angle` 为横竖倾斜角度
3. `rotate(angle)`：旋转元素，`angle` 为旋转角度
4. `scale(x, y)`：缩放元素，`x` 和 `y` 为横竖缩放因子
5. `matrix(a, b, c, d, tx, ty)`：2D 变换矩阵

```CSS
.selector {
    transform: function1(...) function2(...)
}
```

