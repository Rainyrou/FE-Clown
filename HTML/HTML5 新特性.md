###### 新增语义化标签

如 `header`，`footer`、`nav`、`section`、`article`、`aside`、`details`、`summary`、`dialog`、`figure`、`figcaption`、`main`、`mark`、`time`、`date`

###### 表单增强

1. input 类型

- `time`: 时间选择器
- `week`: 选择周和年
- `month`: 月份选择器
- `date`: 日期选择器
- `datetime-local`: 选择日期和时间（无时区）
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

- `autocomplete`: 控制输入字段是否启用自动完成功能
- `autofocus`: 页面加载时自动聚焦到输入字段
- `placeholder`: 在输入字段为空时显示的暗示文字
- `pattern`: 定义输入字段的正则表达式模式
- `required`: 指定输入字段必须填写才能提交表单
- `min` 和 `max`: 定义数字输入或日期输入的最小和最大值
- `multiple`: 允许用户选择多个值

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

###### canvas 绘画

canvas 通过 JavaScript API 提供了绘制 2D 图形的功能

```JavaScript
var canvas = document.getElementById('myCanvas');
var context = canvas.getContext('2d');
context.fillRect(50, 50, 150, 100);
```

###### 内联 svg

SVG（可缩放矢量图形）是一种基于 XML 的矢量图像格式，用于描述二维图形和它们的结构。SVG 的主要优点是其清晰度不受放大缩小的影响，因为它是矢量的

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

Drag and Drop API 提供原生的拖放功能

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

- Geolocation 允许开发者获取用户的地理位置信息

```JavaScript
navigator.geolocation.getCurrentPosition(function(position) {
    console.log("Latitude: " + position.coords.latitude);
    console.log("Longitude: " + position.coords.longitude);
});
```

###### Web Worker

* JavaScript 是单线程语言。Web Worker 允许开发者在后台线程中执行代码，而不会影响主线程的执行，这样可以处理耗时且复杂的任务而不干扰用户界面。Web Worker 不能直接操作 DOM，因此你不能从 Web Worker 内部直接修改页面元素。它们运行在隔离的上下文中，这意味着它们不能访问窗口、文档和父页面的全局函数或变量

###### Web Storage

- 与传统的 cookie 相比，Web 存储提供了更加强大灵活的本地数据存储机制 `localStorage` (持久性存储) 和 `sessionStorage` (会话级存储)

###### WebSocket

- WebSocket 允许服务器和客户端进行实时交互

###### 离线 Web 应用

- 通过创建一个应用缓存 (appcache) 清单，开发者可以指定哪些资源需要被缓存，使应用能够在没有网络连接的情况下工作
