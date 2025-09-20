1px 在设备像素比 > 1 的屏幕上被多个物理像素渲染

1. Viewport：通过设置缩放让 CSS 像素等于物理像素如当设备像素比为 3 时，将页面缩放为 1/3

```js
const scale=1 / window.devicePixelRatio;
const viewport=document.querySelector('meta[name="viewport"]');

if (!viewport) {
    viewport=document.createElement('meta');
    viewport.setAttribute('name', 'viewport');
    window.document.head.appendChild(viewport);
}

viewport.setAttribute('content', 'width=device-width,user-scalable=no,initial-scale=' + scale + ',maximum-scale=' + scale + ',minimum-scale=' + scale);
```

2. border-image：基于媒体判断设备像素比适配对应的 `border-image`

```css
.border_1px {
    border-bottom: 1px solid #000;
}

@media only screen and (-webkit-min-device-pixel-ratio:2) {
    .border_1px {
        border-bottom: none;
        border-width: 0 0 1px 0;
        border-image: url(../img/1pxline.png) 0 0 2 0 stretch;
    }
}
```

3. background-image

```css
.border_1px {
    border-bottom: 1px solid #000;
}

@media only screen and (-webkit-min-device-pixel-ratio:2) {
    .border_1px {
        background: url(../img/1pxline.png) repeat-x left bottom;
        background-size: 100% 1px;
    }
}
```

4. svg

```css
@svg border_1px {
    height: 2px;

    @rect {
        fill: var(--color, black);
        width: 100%;
        height: 50%;
    }
}

.example {
    border: 1px solid transparent;
    border-image: svg(border_1px param(--color #00b1ff)) 2 2 stretch;
}
```

编译后：

```css
.example {
    border: 1px solid transparent;
    border-image: url("data:image/svg+xml;charset=utf-8,%3Csvg xmlns='http://www.w3.org/2000/svg' height='2px'%3E%3Crect fill='%2300b1ff' width='100%25' height='50%25'/%3E%3C/svg%3E") 2 2 stretch;
}
```

5. 伪类 + transform

```css
.border_1px:before {
    content: '';
    position: absolute;
    top: 0;
    height: 1px;
    width: 100%;
    background-color: #000;
    transform-origin: 50% 0%;
}

@media only screen and (-webkit-min-device-pixel-ratio:2) {
    .border_1px:before {
        transform: scaleY(0.5);
    }
}

@media only screen and (-webkit-min-device-pixel-ratio:3) {
    .border_1px:before {
        transform: scaleY(0.33);
    }
}
```
