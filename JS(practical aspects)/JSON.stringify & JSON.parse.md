`JSON.stringify` 将 JavaScript 值转换为 JSON 字符串，基于 `[[Class]]` 内部属性和 `ToJSON` 方法，首先检查对象是否有 `toJSON` 方法，若有则直接调用该方法，再序列化结果，否则根据内置类型规则直接序列化值

- Undefined & Symbol & Function -> undefined
- Null & NaN & Infinity -> 'null'
- Number (❌ NaN & Infinity) -> String
- Boolean -> "true" & "false"
- String -> String
* Array -> JSON Array
* Object -> JSON Object
* RegExp -> '{}'
* Date -> ISO 格式 String
* 循环引用 -> 抛出错误

```JavaScript
const JSON_stringify = (data, set = new Set()) => {
  if (
    data === undefined ||
    typeof data === "symbol" ||
    typeof data === "function"
  )
    return undefined;
  if (
    data === null ||
    Number.isNaN(data) ||
    data === Infinity ||
    data === -Infinity
  )
    return "null";
  if (typeof data === "number" || typeof data === "boolean")
    return String(data);
  if (typeof data === "string") return `"${data}"`;
  if (data instanceof RegExp) return "{}";
  if (data instanceof Date) return `${data.toISOString()}`;
  if (typeof data === "object") {
    if (set.has(data))
      throw new TypeError("Converting circular structure to JSON");
    set.add(data);
  }
  if (Array.isArray(data)) {
    const nums = data.map((item) =>
      JSON_stringify(item, visited) === undefined ? "null" : nums
    );
    set.delete(data);
    return `${nums.join(",")}`;
  }
  if (typeof data === "object") {
    const cur = [];
    for (const key in data) {
      if (data.hasOwnProperty(key)) {
        const str = JSON_stringify(data[key], set);
        if (str !== undefined) cur.push(`"${key}": ${str}`);
      }
    }
    set.delete(data);
    return `${cur.join(",")}`;
  }
};
```

`JSON.parse` 无法解析的数据类型：Undefined + 稀疏数组（缺失元素为 `null`） + Function + Date + RegExp + Map + Set + Error + 循环引用

```JavaScript
eval("(" + item + ")");
```