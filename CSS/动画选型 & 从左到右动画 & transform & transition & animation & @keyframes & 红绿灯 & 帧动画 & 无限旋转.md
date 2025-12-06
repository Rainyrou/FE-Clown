- CSS：声明式，GPU 加速，仅在合成层拼接图层，跳过浏览器布局绘制过程，无需浏览器主线程的 JavaScript
- JavaScript：命令式，在 `requestAnimationFrame` 回调中手动控制动画，运行于浏览器主线程
- Canvas：绘图式，将对象由浏览器渲染引擎转移至 JavaScript 内存中，实现像素级别的任何视觉效果

实现从左到右动画：

- 简单元素平移 -> CSS `transition/animation` + `transform: translateX`
- 复杂逻辑控制 -> JavaScript `requestAnimationFrame/setTimeout/setInterval` 动态修改元素的页面位置
- 复杂图形/大量粒子/游戏场景 -> Canvas 各帧通过  `clearRect`  清空上一帧内容，再通过  `fillRect/drawImage`  绘制元素到新 X 坐标，循环执行

`transform`：基于 2D 变换矩阵运算，元素的外观变化但几何属性不变，触发重绘，不触发回流

- `translate(x, y)`：平移，定义元素在 X 轴和 Y 轴上的位移距离
- `skew(x-angle, y-angle)`：倾斜，定义元素在 X 轴和 Y 轴上的倾斜角度
- `rotate(angle)`：旋转，定义元素顺时针旋转角度
- `scale(x, y)`：缩放，定义元素在 X 轴和 Y 轴上的缩放倍率
- `matrix(a, b, c, d, tx, ty)`：矩阵变换，上述变换的底层实现，通过 2D 变换矩阵控制坐标映射，`a/d` 控制缩放/旋转、`b/c` 控制倾斜、`tx/ty` 控制平移

`transition`：元素样式在不同状态的平滑过渡，需触发条件，其监听指定 CSS 属性变化，自动插入样式插值动画

- `property`：CSS 属性名
- `duration`：执行时间
- `timing-function`：时间曲线如 `linear` 线性、`ease` 先慢后快再慢、`ease-in` 先慢后快、`ease-out` 先快后慢 和 `ease-in-out` 两端慢中间快
- `delay`：延迟等待时间

`animation`：关键帧动画，相比于 `transition`，其无需触发条件，自动运行且提供更细粒度的控制

- `name`：绑定 `@keyframes` 定义的动画名
- `duration`：执行时间
- `timing-function`：时间曲线
- `delay`：延迟等待时间
- `iteration-count`：播放次数如具体数值或 `infinite`
- `direction`：播放方向如 `normal` 正向，`reverse` 反向， `alternate` 先正后反再正，`alternate-reverse` 先反后正再反
- `fill-mode`：执行前后的样式规则，`none` 保持原有样式，`forwards` 停留至最后一帧，`backwards` 停留至第一帧，`both` 同时应用 `forwards/backwards`
- `play-state`：播放状态如 `running` 运行，`paused` 暂停

`@keyframes` 关键帧动画

* 控制粒度：通过百分比或关键字定义
* 样式插值：根据相邻关键帧样式，自动计算并生成中间帧，若某关键帧未定义某属性，则继承其前后关键帧的属性值后再计算插值
* 同名覆盖：同一`@keyframes` 中定义多个相同百分比的关键帧，浏览器合并所有样式规则，根据"后定义的样式覆盖先定义的样式"的层叠顺序计算最终样式，且 `!important` 无效
* JavaScript 动态调整：通过 `CSSKeyframesRule` 接口访问和修改 `@keyframes` 关键帧动画

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

@keyframes spin {
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
}
```
