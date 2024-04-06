1. `querySelector`：只能找作为最后一个子元素的 `p` 标签，若 `div` 的最后一个子元素不是 `p` 标签，这种方法失效

```js
const lastP = document.querySelector("div > p:last-child");
```

2. `lastElementChild`

```js
const pTags = document.querySelector("div").getElementsByTagName("p")
const lastP = pTags[pTags.length - 1]; 
```

3. `querySelectorAll`

```js
const allP = document.querySelectorAll("div > p");
const lastP = allP[allP.length - 1]; 
```