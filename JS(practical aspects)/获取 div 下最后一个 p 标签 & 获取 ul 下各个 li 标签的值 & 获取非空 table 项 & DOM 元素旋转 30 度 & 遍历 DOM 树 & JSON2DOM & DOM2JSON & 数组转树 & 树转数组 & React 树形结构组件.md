- `querySelectorAll` 返回 NodeList（包含元素/文本/注释等节点）
- `getElementById` 返回 HTML 标签对应的节点（元素节点/null）

```js
document.querySelector("div > p:last-child");
const tags = document.querySelector("div").getElementsByTagName("p");
const tags = document.querySelectorAll("div > p");
const lastP = tags[tags.length - 1];
```

获取 ul 下各个 li 标签的值：

```js
document.querySelectorAll("#myList li").forEach((item) => console.log(item));
const items = document.getElementById("myList").getElementsByTagName("li");
for (const item of items) console.log(item);
```

获取非空 table 项：

```js
const getNoEmptyTables = () => {
  const tables = document.querySelectorAll("table");
  const noEmptyTables = Array.from(tables).filter((table) => {
    const trs = table.querySelectorAll("tr");
    return Array.from(trs).some((tr) => {
      const cells = tr.querySelectorAll("td, th");
      return Array.from(cells).some((cell) => cell.textContent.trim() !== "");
    });
  });
  return noEmptyTables;
};

const noEmptyTables = getNoEmptyTables();
console.log(noEmptyTables);
```

DOM 元素旋转 30 度：

```js
document
  .getElementById("element")
  .animate([{ transform: "rotate(0deg)" }, { transform: "rotate(30deg)" }], {
    duration: 1000,
    fill: "forwards",
  });
```

```js
const element = document.getElementById("element");
element.style.transition = "transform 0.5s ease";
requestAnimationFrame(() => (element.style.transform = "rotate(30deg)"));
```

从根节点开始遍历 DOM 树，打印所有节点的 tagName：

```js
const bfs = (dom = document.documentElement) => {
  const queue = [dom];
  while (queue.length) {
    const cur = queue.shift();
    if (cur.nodeType === 1) console.log(cur.tagName);
    Array.from(cur.childNodes).forEach((child) => queue.push(child));
  }
};

bfs();
```

```js
const JSON2DOM = (json) => {
  if (json.type === "text") return document.createTextNode(json.content);
  if (json.type === "comment") return document.createComment(json.content);
  const element = document.createElement(json.tag.toLowerCase());
  for (const key in json.attributes)
    element.setAttribute(key, json.attributes[key]);
  for (const child of json.children) element.appendChild(JSON2DOM(child));
  return element;
}
```

```js
const DOM2JSON = (node) => {
  if (node.nodeType === Node.TEXT_NODE)
    return { type: "text", content: node.textContent };
  if (node.nodeType === Node.COMMENT_NODE)
    return { type: "comment", content: node.textContent };
  const obj = {
    tag: node.tagName.toLowerCase(),
    attributes: {},
    children: [],
  };
  for (const attr of node.attributes) obj.attributes[attr.name] = attr.value;
  for (const child of node.children) obj.children.push(DOM2JSON(child));
  return obj;
};
```

###### 数组转树：

递归：

```js
const arrayToTree = (data, pid) => {
  const result = [];
  const getChildren = (data, result, pid) => {
    for (const item of data) {
      if (item.pid === pid) {
        const newItem = { ...item, children: [] };
        result.push(newItem);
        getChildren(data, newItem.children, item.id);
      }
    }
  };
  getChildren(data, result, pid);
  return result;
};

const testData = [
  { id: 1, name: "根节点1", pid: null },
  { id: 2, name: "根节点2", pid: null },
  { id: 3, name: "子节点1-1", pid: 1 },
  { id: 4, name: "子节点1-2", pid: 1 },
  { id: 5, name: "子节点2-1", pid: 2 },
  { id: 6, name: "子节点2-2", pid: 2 },
  { id: 7, name: "子节点1-1-1", pid: 3 },
  { id: 8, name: "子节点2-1-1", pid: 5 },
];

console.log(JSON.stringify(arrayToTree(testData, null), null, 2));
```

非递归：

```JavaScript
const arrayToTree = (data) => {
  const result = [];
  let map = {};
  for (const item of data) {
    let id = item.id,
      pid = item.pid;
    if (!map[id]) map[id] = { children: [] };
    map[id] = { ...item, children: map[id].children };
    if (!pid) {
      result.push(map[id]);
    } else {
      if (!map[pid]) map[pid] = { children: [] };
      map[pid].children.push(map[id]);
    }
  }
  return result;
};

const testData = [
  { id: 1, name: "根节点1", pid: null },
  { id: 2, name: "根节点2", pid: null },
  { id: 3, name: "子节点1-1", pid: 1 },
  { id: 4, name: "子节点1-2", pid: 1 },
  { id: 5, name: "子节点2-1", pid: 2 },
  { id: 6, name: "子节点2-2", pid: 2 },
  { id: 7, name: "子节点1-1-1", pid: 3 },
  { id: 8, name: "子节点2-1-1", pid: 5 },
];

console.log(JSON.stringify(arrayToTree(testData), null, 2));
```

正确的输出结果：

```json
[
  {
    "id": 1,
    "name": "根节点1",
    "pid": null,
    "children": [
      {
        "id": 3,
        "name": "子节点1-1",
        "pid": 1,
        "children": [
          {
            "id": 7,
            "name": "子节点1-1-1",
            "pid": 3,
            "children": []
          }
        ]
      },
      {
        "id": 4,
        "name": "子节点1-2",
        "pid": 1,
        "children": []
      }
    ]
  },
  {
    "id": 2,
    "name": "根节点2",
    "pid": null,
    "children": [
      {
        "id": 5,
        "name": "子节点2-1",
        "pid": 2,
        "children": [
          {
            "id": 8,
            "name": "子节点2-1-1",
            "pid": 5,
            "children": []
          }
        ]
      },
      {
        "id": 6,
        "name": "子节点2-2",
        "pid": 2,
        "children": []
      }
    ]
  }
]
```

时间复杂度为 O(n)

1. id (Identifier)：节点的唯一标识
2. pid (Parent Identifier)：节点的父节点对应的 `id`，若节点为顶级元素即无父节点，其 `pid` 被设置为一个特定值如 `0`、`null` 或 `undefined`

###### 树转数组

```js
const treeToArray = (data) => {
  let result = [];
  for (const item of data) {
    result.push(item);
    if (item.children) {
      result = result.concat(treeToArray(item.children));
      delete item.children;
    }
  }
  return result;
};

const testData = [
  {
    id: 1,
    name: "根节点1",
    pid: null,
    children: [
      {
        id: 3,
        name: "子节点1-1",
        pid: 1,
        children: [
          {
            id: 7,
            name: "子节点1-1-1",
            pid: 3,
            children: [],
          },
        ],
      },
      {
        id: 4,
        name: "子节点1-2",
        pid: 1,
        children: [],
      },
    ],
  },
  {
    id: 2,
    name: "根节点2",
    pid: null,
    children: [
      {
        id: 5,
        name: "子节点2-1",
        pid: 2,
        children: [
          {
            id: 8,
            name: "子节点2-1-1",
            pid: 5,
            children: [],
          },
        ],
      },
      {
        id: 6,
        name: "子节点2-2",
        pid: 2,
        children: [],
      },
    ],
  },
];

console.log(treeToArray(testData));
```

输出结果

```json
[
  { id: 1, name: '根节点1', pid: null },
  { id: 3, name: '子节点1-1', pid: 1 },
  { id: 7, name: '子节点1-1-1', pid: 3 },
  { id: 4, name: '子节点1-2', pid: 1 },
  { id: 2, name: '根节点2', pid: null },
  { id: 5, name: '子节点2-1', pid: 2 },
  { id: 8, name: '子节点2-1-1', pid: 5 },
  { id: 6, name: '子节点2-2', pid: 2 }
]
```

###### React 树形结构组件

```js
const treeData = {
  list: [
    {
      id: "1",
      name: "folder1",
      children: [
        {
          id: "11",
          name: "file1",
        },
        {
          id: "12",
          name: "file2",
        },
      ],
    },
    {
      id: "2",
      name: "folder2",
      children: [
        {
          id: "21",
          name: "folder3",
          children: [
            {
              id: "211",
              name: "file3",
            },
          ],
        },
      ],
    },
    {
      id: "3",
      name: "file4",
    },
  ],
};

const TreeNode = ({ node }) => {
  return (
    <>
      {node.name}
      {node?.children?.map?.((child) => (
        <TreeNode key={child.id} node={child} />
      ))}
    </>
  );
};

export const App = () => {
  return (
    <>
      {treeData.list.map((node) => (
        <TreeNode key={node.id} node={node} />
      ))}
    </>
  );
};
```
