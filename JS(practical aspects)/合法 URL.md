[learn-regex/translations/README-cn.md at master · ziishaned/learn-regex · GitHub](https://github.com/ziishaned/learn-regex/blob/master/translations/README-cn.md)

```js
const isUrl = (url) =>
  /^((https|http)?:\/\/)((([A-Za-z0-9]+-[A-Za-z0-9]+|[A-Za-z0-9]+)\.)+([A-Za-z]{2,6})|(\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}))(:\d+)?(\/.*)?(\?.*)?(#.*)?$/.test(
    url
  );

console.log(isUrl("http://114.132.167.29:5021/")); // true
console.log(
  isUrl(
    "https://github.com/ziishaned/learn-regex/blob/master/translations/README-cn.md"
  ) // true
);
```