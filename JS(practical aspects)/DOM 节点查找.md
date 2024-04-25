```js
const parentNode = (node1, node2) => {
  while (true) {
    if (node1.contains(node2)) return node1;
    node1 = node1.parentNode;
  }
};
```