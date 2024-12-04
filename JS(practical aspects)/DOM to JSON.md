DOM 树：

```html
<html>
<body>
    <div>
        <span>
            <div>Text 1</div>
            <div>Text 2</div>
        </span>
        <span>
            <div>Text 3</div>
            <div>Text 4</div>
        </span>
    </div>
</body>
</html>
```

在浏览器控制台输入：

```js
const DOMtoJSON = (node) => {
  const obj = {
    tag: node.tagName.toLowerCase(),
    children: [],
  };
  Array.from(node.children).forEach((item) =>
    obj.children.push(DOMtoJSON(item))
  );
  return obj;
};

const json = DOMtoJSON(document.body);
console.log(JSON.stringify(json, null, 2)); // 目标对象 + 指定哪些键被包含于 JSON 字符串 + 格式化 JSON 字符串
```

JSON 对象：

```JSON
{
  "tag": "body",
  "children": [
    {
      "tag": "div",
      "children": [
        {
          "tag": "span",
          "children": [
            {
              "tag": "div",
              "children": []
            },
            {
              "tag": "div",
              "children": []
            }
          ]
        },
        {
          "tag": "span",
          "children": [
            {
              "tag": "div",
              "children": []
            },
            {
              "tag": "div",
              "children": []
            }
          ]
        }
      ]
    }
  ]
}
```
