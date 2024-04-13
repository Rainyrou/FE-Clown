```js
const DOMtoJSON = (node) => {
  const obj = {
    tag: node.tagName.toLowerCase(),
    children: [],
  };
  node.children.forEach((item) => obj.children.push(DOMtoJSON(item)));
  return obj;
};
```