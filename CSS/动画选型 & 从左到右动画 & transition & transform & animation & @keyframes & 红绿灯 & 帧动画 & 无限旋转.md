- CSS：声明式，GPU 加速，仅在合成层拼接图层，跳过浏览器布局绘制过程，无需浏览器主线程的JavaScript
- JavaScript：命令式，在 `requestAnimationFrame` 回调中手动控制动画，运行于浏览器主线程
- Canvas：绘图式，将对象由浏览器渲染引擎转移至JavaScript 内存中，实现像素级别的任何视觉效果

实现从左到右动画：

- 简单元素平移 -> CSS `transition/animation` + `transform: translateX`
- 复杂逻辑控制 -> JavaScript `requestAnimationFrame/setTimeout/setInterval` 动态修改元素的页面位置
- 复杂图形/大量粒子/游戏场景 -> Canvas 各帧通过  `clearRect`  清空上一帧内容，再通过  `fillRect/drawImage`  绘制元素到新 X 坐标，循环执行

`transition`  用于在不同状态间平滑过渡元素样式即在某特定时刻动态化改变 CSS 属性

- `property`：定义过渡效果的 CSS 属性名如  `opacity`、`background-color`  等
- `duration`：定义过渡效果的执行时间
- `timing-function`：定义过渡效果的时间曲线如  `linear`、`ease`、`ease-in`、`ease-out`  和  `ease-in-out`，可通过  `cubic-bezier`  函数自定义
- `delay`：定义延迟即在应用过渡效果前的等待时间

```css
.selector {
    transition: property duration timing-function delay;
}
```

`transform`  用于对元素进行平移、倾斜、旋转和缩放以改变其形状和位置

- `translate(x, y)`：移动元素，`x`  和  `y`  为横竖移动距离
- `skew(x-angle, y-angle)`：倾斜元素，`x-angle`  和  `y-angle`  为横竖倾斜角度
- `rotate(angle)`：旋转元素，`angle`  为旋转角度
- `scale(x, y)`：缩放元素，`x`  和  `y`  为横竖缩放因子
- `matrix(a, b, c, d, tx, ty)`：2D 变换矩阵

```css
.selector {
    transform: function1(...) function2(...)
}
```

`animation` 用于定义动画

- `name`：定义动画名，与 `@keyframes` 中定义的关键帧名相关联
- `duration`：定义动画执行时间
- `timing-function`：定义动画时间曲线
- `delay`：定义延迟即在应用动画效果前的等待时间
- `iteration-count`：定义动画播放次数
- `direction`：定义动画播放方向
- `fill-mode`：定义动画执行前后影响元素样式的方式
- `play-state`：定义动画播放状态

`@keyframes` 通过定义一系列关键帧样式描述动画步骤及过渡效果，相比于 `transition`，其提供对动画中间步骤更细粒度的控制，定义多步渐变效果

控制动画步骤： 每个动画分为多个关键帧，通过百分比或关键字如 `from` 和 `to` 定义动画从开始到结束的状态
样式插值： CSS 根据不同关键帧指定样式，自动计算并生成中间状态即插值，若某关键帧未定义某属性，则在中间帧中使用其前后帧的插值
绑定动画：通过 `animation-name` 绑定动画并结合 `animation-duration` 和 `animation-timing-function` 等来控制动画
重复定义：若在同一 `@keyframes` 中定义多个相同百分比的关键帧，则浏览器使用所有关键帧并依照层叠顺序计算样式
`!important` 无效：关键帧的样式层叠机制不同于原生的 CSS 规则
JavaScript 动态操作：通过 DOM 的 `CSSKeyframesRule` 接口访问和动态创建 `@keyframes` 动画

红绿灯：

```css
.traffic-light {
    width: 100px;
    height: 250px;
    padding: 10px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
    background-color: #333;
    border-radius: 20px;
}

.light {
    width: 60px;
    height: 60px;
    border-radius: 50%;
    background-color: gray;
    opacity: 0.3;
}

@keyframes red-light {
    0% {
        background-color: red;
        opacity: 1;
    }

    50% {
        background-color: white;
        opacity: 0.3;
    }

    100% {
        background-color: red;
        opacity: 1;
    }
}

@keyframes green-light {
    0% {
        background-color: green;
        opacity: 1;
    }
    50% {
        background-color: white;
        opacity: 0.3;
    }
    100% {
        background-color: green;
        opacity: 1;
    }
}

@keyframes yellow-light {
    0% {
        background-color: yellow;
        opacity: 1;
    }
    50% {
        background-color: white;
        opacity: 0.3;
    }
    100% {
        background-color: yellow;
        opacity: 1;
    }
}

.red {
    animation: red-light 6s infinite;
}
.green {
    animation: green-light 6s infinite 2s;
}
.yellow {
    animation: yellow-light 6s infinite 4s;
}
```

帧动画：

```css
.sprite-animation {
  width: 100px;
  height: 100px;
  background-image: url('xxx.png');
  animation: play-frame 0.6s step(6) infinite;
}

@keyframes play-frame {
  from {
    background-position: 0 0;
  }

  to {
    background-position: -600px 0;
  }
}
```

帧动画的关键为逐帧跳转而非平滑过渡：

- `timing-function: step-end/steps(n, end)`）：动画在各帧结束时直接跳转至下一状态，无中间过渡

* `@keyframes`  定义各帧的固定状态，帧与帧间无渐变

```css
.sprite-animation {
  width: 100px;
  height: 100px;
  animation: spin 2s linear infinite;
}

@keyframs spin {
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
}
```
