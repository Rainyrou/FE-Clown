解析 Markdown 标题语法为指定格式文本：

```js
const parseHeadings = (markdownText, format = "html") =>
  markdownText.replace(/^(#{1,6})\s+(.+)$/gm, (all, hashes, content) => {
    const level = hashes.length,
      text = content.trim();
    if (format === "html") return `<h${level}>${text}</h${level}>`;
    if (typeof format === "function") return format(level, text);
    return all;
  });

const md = `
# 这是 H1
## 这是 H2
这是一个段落。
###   这是 H3（前面有空格）
无效的 # 标题
####### 这不是 H7
`;
const htmlOutput = parseHeadings(md, "html");
console.log(htmlOutput);

/*
<h1>这是 H1</h1>
<h2>这是 H2</h2>
这是一个段落。
<h3>这是 H3（前面有空格）</h3>
无效的 # 标题
####### 这不是 H7
*/

const customFormat = (level, text) => `[标题 ${level}] - ${text}`;
const customOutput = parseHeadings(md, customFormat);
console.log(customOutput);

/*
[标题 1] - 这是 H1
[标题 2] - 这是 H2
这是一个段落。
[标题 3] - 这是 H3（前面有空格）
无效的 # 标题
####### 这不是 H7
*/
```

模板引擎：

```js
String.prototype.render = function (data) {
  return this.replace(/{{\s*(#?[\s\S]*?)\s*}}/g, (all, match) => {
    try {
      if (match.startsWith("#"))
        return eval(`with (data) { ${match.slice(1)} }`);
      return data.hasOwnProperty(match.trim()) ? data[match.trim()] : "";
    } catch (err) {
      console.error(err);
      return "";
    }
  });
};
```

在正则表达式中：

- 贪婪匹配：尽可能多地匹配字符直至无法再匹配为止，符号 `*`、`+` 和 `?` 等默认为贪婪的
- 非贪婪匹配：尽可能少地匹配字符直至匹配到满足条件之最小结果，通过 `?` 可将贪婪匹配转换为非贪婪匹配如 `.*` 为贪婪匹配，而 `.*?` 为非贪婪匹配

- `{{` 和 `}}` -> 占位符之界定符
- `\s*` -> 任意数量空白字符
- `#?` -> 可选 `#` 用于区分表达式和数据占位符
- `[\s\S]*?` -> 非贪婪匹配任意字符包括空白字符 `\s` 和非空白字符 `\S`
- `eval` 执行 JavaScript 代码
- `with` 将 `data` 对象属性提升至作用域链顶部，直接以 `xxx` 的形式访问 `data` 对象属性而无需以 `data.xxx` 的形式

测试用例：

```js
const data = {
  name: "小明",
  age: 16,
  school: "第三中学",
  classroom: "教室2",
};

console.log(
  "{{ name }} 今年 {{ age }} 岁，就读于 {{ school }} 今天在 {{ classroom }} 上课，{{ name }} {{ #data.age >= 18 ? '成年了' : '未成年' }}".render(
    data
  )
);
// 小明 今年 16 岁，就读于 第三中学 今天在 教室2 上课，小明 未成年

console.log(
  `{{name}}说了句{{#
      if (data.age >= 18) {
          "我已经成年了！"
      } else {
          "我还没有成年！"
      }
  }}`.render(data)
);
// 小明说了句我还没有成年！
```

模板语法：

```js
const template = (str, data) =>
  str.replace(/<%(\=)?\s*([\s\S]*?)\s*%>/g, (all, match1, match2) => {
    try {
      if (match1) return eval(`with (data) { ${match2.trim()} }`);
      else return data.hasOwnProperty(match2.trim()) ? data[match2.trim()] : "";
    } catch (err) {
      console.error(err);
      return "";
    }
  });

console.log(
  template(
    `Hello, <%= name%>, I am a <%= role %> > <% if (name === 'a') { %><% } %>`,
    { name: "stephen", role: "engineer" }
  )
);
// Hello, stephen, I am a engineer
```