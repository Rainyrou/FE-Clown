RGBA 颜色模型代表 Red + Green + Blue + Alpha 透明度，它扩展了传统的 RGB 颜色模型，添加一个 Alpha 通道来控制颜色透明度，允许颜色与背景的混合。`rgba` 函数接受四个参数：红绿蓝的值 0-255 范围内和 Alpha 值 0-1 范围内，0 完全透明，1 完全不透明

颜色转换是指将其他颜色格式如 HEX 转换为 RGBA 格式。HEX 颜色格式以 `#` 开头，后跟 6 位十六进制数，前两位代表红，中间两位代表绿，最后两位代表蓝。转换时，需从 HEX 格式的十六进制数值中提取相应的红绿蓝三色的十六进制值，将它们转换为十进制数值，再结合给定的 Alpha 值，使用 `rgba` 函数合成最终的颜色字符串

```JavaScript
const hexToRgba = (hex, alpha = 1) => {
  let r = 0,
    g = 0,
    b = 0;
  if (hex.length === 4) {
    r = parseInt(hex[1] + hex[1], 16);
    g = parseInt(hex[2] + hex[2], 16);
    b = parseInt(hex[3] + hex[3], 16);
  } else if (hex.length === 7) {
    r = parseInt(hex[1] + hex[2], 16);
    g = parseInt(hex[3] + hex[4], 16);
    b = parseInt(hex[5] + hex[6], 16);
  }
  return `rgba(${r}, ${g}, ${b}, ${alpha})`;
};
```
