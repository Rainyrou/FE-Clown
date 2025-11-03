```js
const trim = (str) => str.replace(/^\s+|\s+$/g, "");
const testStr = "   \n\t  Hello World   \r\n ";
console.log(trim(testStr)); // Hello World
```