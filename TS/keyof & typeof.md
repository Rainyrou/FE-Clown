`keyof`：用于获取某个类型的所有公共属性名作为联合类型

```js
interface Person {
    name: string;
    age: number;
}

type PersonKeys = keyof Person; // 'name' | 'age'
```

`typeof`：用于在编译时获取数据类型，不同于 JS 的 `typeof`

```js
let person = {
    name: "Alice",
    age: 25
};

type PersonType = typeof person;  // { name: string; age: number; }
```

