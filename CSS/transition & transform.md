##### transition

`transition` 属性用于在不同状态之间平滑过渡元素的样式，即在某个特定时间段内动画化地改变 CSS 属性

1. `transition-property`：指定应用过渡效果的 CSS 属性名称。如 `opacity`、`background-color` 等。如果你想对所有可过渡的属性应用相同的过渡效果，可以使用 `all`
2. `transition-duration`：定义过渡效果执行的时间，以秒（s）或毫秒（ms）为单位
3. `transition-timing-function`：定义过渡效果的时间曲线，包括 `linear`、`ease`、`ease-in`、`ease-out` 和 `ease-in-out`。此外还可以使用 `cubic-bezier` 函数来自定义曲线
4. `transition-delay`：设置延迟，即在开始应用过渡效果之前等待的时间，以秒（s）或毫秒（ms）为单位

```CSS
.selector {
	transition: property duration timing-function delay;
}
```

###### 实例

当鼠标悬停在元素上时，它将改变背景色并放大，过渡持续时间为 0.3 秒：

`index.html`

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>scale</title>
</head>
<body>
    <button class="scale">click</button>
    <link rel="stylesheet" href="style.css">
</body>
</html>
```

`style.css`

```CSS
.scale: {
	width: 100px;
	height: 100px;
	background-color: blue;
	transition: background-color 0.3s ease, transform 0.3s ease;
}

.scale:hover {
	background-color: red;
	transform: scale(2);
}
```

##### transform

`transform` 属性允许你对元素进行平移、倾斜、旋转、缩放，从而改变元素的形状和位置

1. `translate(x, y)`：移动元素，`x` 和 `y` 是横向和纵向的移动距离，可以是长度值（如 px、em）或百分比
2. `skew(x-angle, y-angle)`：倾斜元素，`x-angle` 和 `y-angle` 分别是横向和纵向的倾斜角度
3. `rotate(angle)`：旋转元素，`angle` 是旋转角度，单位可以是度（deg）或弧度（rad）。如 `rotate(45deg)` 表示顺时针旋转 45 度
4. `scale(x, y)`：缩放元素，`x` 和 `y` 分别是横向和纵向的缩放因子。`scale(1, 1)` 保持元素原始大小，`scale(2, 2)` 使元素大小变为原来的两倍
5. `matrix(a, b, c, d, tx, ty)`：应用一个 2D 变换矩阵，允许你直接操作元素的坐标系统

```CSS
.selector {
	transform: function1(...) function2(...)
}
```

###### 注意事项

- `transform` 的默认值是 `none`，表示没有进行任何变换
- 在使用 `transform` 时，元素会变成 BFC 的一部分

###### 实例

`index.html`

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>scale</title>
</head>
<body>
    <button class="scale">click</button>
    <link rel="stylesheet" href="style.css">
</body>
</html>
```

`style.css`

```CSS
.scale {
    width: 100px;
    height: 100px;
    background-color: blue;
    transition: transform 0.3s ease;
}

.scale:hover {
    transform: rotate(45deg) scale(2);
}
```
