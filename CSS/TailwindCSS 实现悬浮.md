```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TailwindCSS Hover</title>
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
    <style>
        /* 由于 TailwindCSS 2.x 不直接支持在 HTML 类中使用悬浮状态改变子元素的样式，
     我们需要添加一些自定义 CSS 来实现这一效果 */
        .parent:hover .child {
            background-color: #4ade80;
            color: #fff;
        }
    </style>
</head>
<body>
    <div
        class="parent p-6 max-w-sm mx-auto bg-white rounded-xl shadow-md flex items-center space-x-4 hover:bg-blue-500 cursor-pointer">
        <div class="child flex-shrink-0">
            <img class="h-12 w-12" src="https://tailwindcss.com/img/logo.svg" alt="Tailwind">
        </div>
        <div>
            <div class="text-xl font-medium text-black child">TailwindCSS</div>
            <p class="text-gray-500 child">Hover</p>
        </div>
    </div>
</body>
</html>
```

