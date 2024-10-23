在浏览器中输入

模拟 `React.createElement`，`tag` 为 HTML 标签，`props` 为元素属性，`document.createTextNode` 创建文本节点

```js
function createElement(tag, props, ...children) {
  return { tag, props, children };
}

function render(vDom) {
  const element = document.createElement(vDom.tag);
  if (vDom.props)
    Object.keys(vDom.props).forEach((key) =>
      element.setAttribute(key, vDom.props[key])
    );
  vDom.children.forEach((child) =>
    element.appendChild(
      typeof child === "string" ? document.createTextNode(child) : render(child)
    )
  );
  return element;
}

function diff(oldVdom, newVdom) {
  if (!oldVdom) return newVdom;
  if (!newVdom) return null;
  if (typeof oldVdom === "string" || typeof newVdom === "string")
    return oldVdom !== newVdom ? newVdom : null;
  if (oldVdom.tag !== newVdom.tag) return newVdom;
  const patchProps = {};
  Object.keys(newVdom.props || {}).forEach((key) => {
    if (newVdom.props[key] !== oldVdom.props[key])
      patchProps[key] = newVdom.props[key];
  });
  Object.keys(oldVdom.props || {}).forEach((key) => {
    if (!newVdom.props[key]) patchProps[key] = null;
  });
  const patchChildren = [];
  const maxChildrenLength = Math.max(
    oldVdom.children.length,
    newVdom.children.length
  );
  for (let i = 0; i < maxChildrenLength; ++i)
    patchChildren.push(diff(oldVdom.children[i], newVdom.children[i]));
  return { ...newVdom, props: patchProps, children: patchChildren };
}

function patch(parent, oldVdom, newVdom, index = 0) {
  const element = parent.childNodes[index];
  if (!oldVdom && newVdom) parent.appendChild(render(newVdom));
  else if (oldVdom && !newVdom && element) parent.removeChild(element);
  else if (
    (typeof oldVdom === "string" || typeof newVdom === "string") &&
    oldVdom !== newVdom &&
    element
  )
    element.textContent = newVdom;
  else if (oldVdom.tag !== newVdom.tag) {
    if (element) parent.replaceChild(render(newVdom), element);
    else {
      Object.keys(newVdom.props || {}).forEach((key) => {
        if (oldVdom.props[key] !== newVdom.props[key] && element)
          element.setAttribute(key, newVdom.props[key]);
      });
      Object.keys(oldVdom.props || {}).forEach((key) => {
        if (!newVdom.props[key] && element) element.removeAttribute(key);
      });
      const maxChildrenLength = Math.max(
        oldVdom.children.length,
        newVdom.children.length
      );
      for (let i = 0; i < maxChildrenLength; ++i) {
        if (element)
          patch(element, oldVdom.children[i], newVdom.children[i], i);
      }
    }
  }
}

const vDom1 = createElement(
  "div",
  { id: "app" },
  createElement("h1", null, "Hello"),
  createElement("p", null, "Remove me")
);

const vDom2 = createElement(
  "div",
  { id: "app" },
  createElement("h1", null, "Hello")
);

const realDom1 = render(vDom1);
document.body.appendChild(realDom1);
console.log(realDom1);
patch(document.body, vDom1, vDom2);
```
