`JSON.stringify`  将 JavaScript 值转换为 JSON 字符串，其转换过程基于 `[[Class]]` 内部属性和 `ToJSON` 方法，对于内置类型，`JSON.stringify` 首先检查对象是否有 `toJSON` 方法，若有，直接调用该方法，再对结果值进行序列化，若无，`JSON.stringify` 根据内置类型规则直接序列化值

- Undefined & Symbol & Function -> undefined
- Null & NaN & Infinity -> null
- Number (❌ NaN & Infinity) -> String
- Boolean -> "true" & "false"
- String -> String
* Array -> JSON Array
* Object -> JSON Object
* RegExp -> {}
* Date -> ISO 格式 String
* 循环引用 -> 抛出错误

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

输出结果：

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
