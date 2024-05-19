```js
class TreeNode {
  constructor(value) {
    this.value = value;
    this.children = [];
  }
  addChild(child) {
    this.children.add(child);
  }
}

const dfs = (node) => {
  if (!node) return;
  console.log(node);
  for (const child of node.children) dfs(child);
};

const bfs = (node) => {
  if (!node) return;
  const queue = [node];
  while (queue.length) {
    const cur = queue.shift();
    console.log(cur);
    for (const child of cur.children) queue.push(child);
  }
};
```