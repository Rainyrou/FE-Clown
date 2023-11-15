`@media` 规则应用于不同样式，以适应不同设备类型和屏幕尺寸，它是响应式设计的一个关键特性

1. 屏幕宽高 `width` 和 `height`：`@media (min-width: 600px) { ... }`

2. 设备宽高 `device-width` 和 `device-height`：`@media (min-device-width: 800px) { ... }`

3. 方向（横纵） `orientation`：`@media (orientation: landscape) { ... }`

4. 纵横比 `aspect-ratio`：`@media (aspect-ratio: 16/9) { ... }`

5. 分辨率 `resolution`：`@media (min-resolution: 2dppx) { ... }`

6. 颜色 `color`：`@media (min-color: 2) { ... }`

7. 打印 `print`：`@media print { ... }`

###### 示例

```CSS
/* 对于宽度小于600px的设备 */
@media (max-width: 599px) {
  body {
    background-color: lightblue;
  }
}

/* 对于宽度至少为600px的设备 */
@media (min-width: 600px) {
  body {
    background-color: pink;
  }
}
```
