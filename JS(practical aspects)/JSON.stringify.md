`JSON.stringify()`  方法是将一个 JavaScript 值(对象或者数组)转换为 JSON 字符串。此处模拟实现不考虑可选的第二个参数  `replacer`  和第三个参数  `space`

1. 基本数据类型及函数：
- string 转换之后仍是 string
- undefined、symbol 和 function 转换之后是 undefined
- number 类型(除 NaN 和 Infinity)转换之后是 string 类型数值
- NaN  和  Infinity  转换之后是字符串"null"
- boolean 转换之后是字符串"false"或者"true"

2. 对象类型（不包括函数）：
- null 转换之后是字符串"null"
* array：如果属性值中出现 undefined、symbol 或函数，转换之后是字符串"null"
* RegExp：返回  `{}` (类型是 string)
* Date：调用 toJSON()，转化之后是字符串
* 如果是普通对象：
  - 如果有 toJSON()，序列化 toJSON()的返回值
  - 以 symbol 为属性键的属性忽略
  - 如果属性值中出现  undefined、symbol 或者 function，忽略

4. 对包含循环引用的对象执行此方法，会抛出错误

```JavaScript
function jsonStringify(data) {
    let dataType = typeof data;
    if(dataType !== 'object') {
        let result = data;
        if(Number.isNaN(data) || data === Infinity) return "null";
        else if(dataType === 'undefined' || dataType === 'symbol' || dataType === 'function') return undefined;
        else if(dataType === 'string') result = '"' + result + '"';
        return String(result);
    } else if(dataType === 'object') {
        if(data === null) return "null";
        else if(data.toJSON && typeof data.toJSON === 'function') return jsonStringify(data.toJSON());
        else if(data instanceof Array) {
            let result = [];
            data.forEach((item, index) => {
                if(typeof item === 'undefined' || typeof item === 'symbol' || typeof item === 'function') result[index] = "null";
                else result[index] = jsonStringify(item);
            });
            result = "[" + result + "]";
            return result.replace(/'/g, '"');
        } else {
            let result = [];
            Object.keys(data).forEach((item, index) => {
                if(typeof item !== 'symbol') {
                    if(typeof data[item] !== 'undefined' && typeof data[item] !== 'symbol' && typeof data[item] !== 'function') {
                        result.push('"' + item + '"' + ":" + jsonStringify(data[item]));
                    }
                }
            })
            result = "{" + result + "}";
            return result.replace(/'/g, '"');
        }
    }
}
```

测试用例：

```JavaScript
let sym = Symbol(10);
console.log(jsonStringify(sym) === JSON.stringify(sym));
let nul = null;
console.log(jsonStringify(nul) === JSON.stringify(nul));
let und = undefined;
console.log(jsonStringify(undefined) === JSON.stringify(undefined));
let boo = false;
console.log(jsonStringify(boo) === JSON.stringify(boo));
let nan = NaN;
console.log(jsonStringify(nan) === JSON.stringify(nan));
let inf = Infinity;
console.log(jsonStringify(Infinity) === JSON.stringify(Infinity));
let str = "hello";
console.log(jsonStringify(str) === JSON.stringify(str));
let reg = new RegExp("\w");
console.log(jsonStringify(reg) === JSON.stringify(reg));
let date = new Date();
console.log(jsonStringify(date) === JSON.stringify(date));
let obj = {
    name: '刘小夕',
    age: 22,
    hobbie: ['coding', 'writing'],
    date: new Date(),
    unq: Symbol(10),
    sayHello: function() {
        console.log("hello")
    },
    more: {
        brother: 'Star',
        age: 20,
        hobbie: [null],
        info: {
            money: undefined,
            job: null,
            others: []
        }
    }
}
console.log(jsonStringify(obj) === JSON.stringify(obj));
function SuperType(name, age) {
    this.name = name;
    this.age = age;
}
let per = new SuperType('小姐姐', 20);
console.log(jsonStringify(per) === JSON.stringify(per));
function SubType(info) {
    this.info = info;
}
SubType.prototype.toJSON = function() {
    return {
        name: '钱钱钱',
        mount: 'many',
        say: function() {
            console.log('我偏不说！');
        },
        more: null,
        reg: new RegExp("\w")
    }
}
let sub = new SubType('hi');
console.log(jsonStringify(sub) === JSON.stringify(sub));
let map = new Map();
map.set('name', '小姐姐');
console.log(jsonStringify(map) === JSON.stringify(map));
let set = new Set([1,2,3,4,5,1,2,3]);
console.log(jsonStringify(set) === JSON.stringify(set));
```

正确的输出结果

```
true
true
true
true
true
true
true
true
true
true
true
true
true
true
```
