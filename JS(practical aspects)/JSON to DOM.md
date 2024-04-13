```js
const render = (vnode) => {
  const el = document.createElement(vnode.tag.toLocaleLowerCase());
  for (const item in vnode.attrs) el.setAttribute(item, vnode.attrs[item]);
  if (typeof vnode.children === "string") {
    const textNode = document.createTextNode(vnode.children);
    el.appendChild(textNode);
  }
  if (Array.isArray(vnode.children) && vnode.children.length) {
    vnode.children.forEach((child) => el.appendChild(render(child, el)));
  }
  return el;
};
```