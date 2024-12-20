```
input

video/audio

canvas/svg

ondrag/ondrop

geolocation

webworker/websocket/webstorage
```

###### 新增语义化标签

如 `main`、`header`，`footer`、`nav`、`section`、`article`、`aside`、`details`、`summary`、`dialog`、`figure`、`figcaption`、`mark`、`time`、`date`

###### 表单增强

1. input 类型 

- `time`: 时间选择器
- `date`: 日期选择器
- `datetime-local`: 选择日期和时间（无时区）
- `month`: 月份选择器
- `week`: 选择周和年
- `number`: 数字输入，可以设置最大值和最小值
- `range`: 滑块，用于从范围中选择数字
- `search`: 专为搜索字段设计
- `email`: 针对电子邮件地址的输入字段
- `tel`: 电话号码输入字段
- `url`: 用于输入 URL 的字段
- `color`: 颜色选择器

2. 表单元素

- `<datalist>`: 与 `list` 属性结合使用，为输入字段提供一个预定义的选项列表
* `<keygen>`: 用于验证用户，表单生成密钥对
- `<output>`: 表示计算结果

3. 表单属性

- `placeholder`: 在输入字段为空时显示的暗示文字
- `pattern`: 定义输入字段的正则表达式模式
- `required`: 指定输入字段必须填写才能提交表单
- `multiple`: 允许用户选择多个值
- `autocomplete`: 控制输入字段是否启用自动完成功能
- `autofocus`: 页面加载时自动聚焦到输入字段
- `min` 和 `max`: 定义数字输入或日期输入的最小和最大值

###### 多媒体支持

- `<audio>`: 定义音频内容
- `<video>`: 定义视频内容
- `<source>`: 定义多媒体资源路径
- `<embed>`: 定义嵌入的内容，比如插件
- `<track>`: 定义引入字幕文件或其他包含文本的文件

```JavaScript
<video controls>
    <source src="movie.mp4" type="video/mp4">
    <source src="movie.ogg" type="video/ogg">
    Your browser does not support the video tag.
</video>
```

###### canvas 

```JavaScript
var canvas = document.getElementById('myCanvas');
var context = canvas.getContext('2d');
context.fillRect(50, 50, 150, 100);
```

###### svg

```JavaScript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Inline SVG Example</title>
</head>
<body>
    <svg width="100" height="100">
        <circle cx="50" cy="50" r="40" stroke="black" stroke-width="3" fill="red" />
    </svg>
</body>
</html>
```

###### Drag and Drop API

```JavaScript
<div draggable="true" ondragstart="drag(event)">Drag</div>
<script type="text/javascript">
    function drag(e){
        console.log(e);
    }
</script>
```

- `ondrag`: 当拖动元素或选中文本时触发
- `ondragend`: 当拖拽操作结束时触发，如松开鼠标按键或敲 Esc 键
- `ondragenter`: 当拖动元素或选中文本到一个可释放目标时触发
- `ondragexit`: 当元素不再是拖动操作的选中目标时触发
- `ondragleave`: 当拖动元素或选中文本离开一个可释放目标时触发
- `ondragover`: 当元素或选中文本被拖到一个可释放目标上时触发，每 100 毫秒触发一次
- `ondragstart`: 当用户开始拖动一个元素或选中文本时触发
- `ondrop`: 当元素或选中文本在可释放目标上被释放时触发

###### 地理位置

Geolocation 允许开发者获取用户的地理位置信息

```JavaScript
navigator.geolocation.getCurrentPosition(function(position) {
    console.log("Latitude: " + position.coords.latitude);
    console.log("Longitude: " + position.coords.longitude);
});
```

###### WebSocket & Web Worker & Web Storage
