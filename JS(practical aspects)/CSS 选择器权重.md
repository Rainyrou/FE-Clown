```js
const getSelectorWeight = (selector) => {
  const a = 0;
  const cleanSelector = selector.replace(/\s*\*\s*/g, "");
  const idRegExp = /#\w+/g;
  const classOrPseudoOrAttrRegExp = /(\.\w+)|(\[\w+.*?\])|(:\w+)/g;
  const tagOrPseudoElementRegExp = /(\w+)|(::\w+)/g;
  const idSelector = cleanSelector.match(idRegExp) || [];
  const b = idSelector.length;
  const classOrPseudoOrAttrSelector =
    cleanSelector.match(classOrPseudoOrAttrRegExp) || [];
  const c = classOrPseudoOrAttrSelector.length;
  const remainSelector = cleanSelector
    .replace(idRegExp, " ")
    .replace(classOrPseudoOrAttrRegExp, " ");
  const tagOrPseudoElementSelector =
    remainSelector.match(tagOrPseudoElementRegExp) || [];
  const d = tagOrPseudoElementSelector.length;
  return [a, b, c, d];
};

const compareSelectors = (selector1, selector2) => {
  const weight1 = getSelectorWeight(selector1),
    weight2 = getSelectorWeight(selector2);
  for (let i = 0; i < 4; ++i) {
    if (weight1[i] > weight2[i]) return 1;
    else if (weight1[i] < weight2[i]) return -1;
  }
  return 0;
};

console.log(compareSelectors("#id", ".class")); // 1
console.log(compareSelectors(".class1.class2", "#id")); // -1
console.log(compareSelectors("div#id", "#id")); // 1
console.log(compareSelectors(".class:nth-child(2)", "#id")); // -1
console.log(compareSelectors("div.class", ".class")); // 1
console.log(compareSelectors("div", "span")); // 0
```