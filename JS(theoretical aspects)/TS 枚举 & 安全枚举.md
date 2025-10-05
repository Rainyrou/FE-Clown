###### 枚举

TypeScript 的枚举有数字枚举、字符串枚举和常量枚举，数字枚举默认从0开始递增，也可手动设置初始值，而字符串枚举各个成员必须以字符串字面量初始化

TypeScript 的枚举在底层通过生成一个双向映射的对象来实现，对于数字枚举来说，既支持从名称到值的查找，也支持从值到名称的反向查找，而字符串枚举则只保留名称到值的映射，常量枚举则在编译时被替换为字面量，不生成运行时代码，无法在运行时反射枚举值

1. 数字枚举：

```ts
enum Direction {
  Up,
  Down,
  Left,
  Right,
}
```

编译后的 JavaScript：

```js
var Direction;
(function (Direction) {
  Direction[(Direction["Up"] = 0)] = "Up";
  Direction[(Direction["Down"] = 1)] = "Down";
  Direction[(Direction["Left"] = 2)] = "Left";
  Direction[(Direction["Right"] = 3)] = "Right";
})(Direction || (Direction = {}));
```

2. 字符串枚举

```ts
enum Direction {
  Up = "UP",
  Down = "DOWN",
}
```

编译后的 JavaScript：

```js
var Direction;
(function (Direction) {
  Direction["Up"] = "UP";
  Direction["Down"] = "DOWN";
})(Direction || (Direction = {}));
```

3. 常量枚举

```ts
const enum Direction {
  Up,
  Down,
}

let dir = Direction.Up;
```

编译后的 JavaScript：

```js
let dir = 0 
```

###### 安全枚举

1. 字面量类型联合

```ts
type SafeDirection = "north" | "south" | "west" | "east";

const move = (direction: SafeDirection) => console.log(`Moving ${direction}`);
move("north");
move("south");
move("up"); // Argument of type '"up"' is not assignable to parameter of type 'SafeDirection'.
```

2. 对象字面量 + as const

```ts
const Direction = {
  North: "north",
  East: "east",
  South: "south",
  West: "west",
} as const;

type SafeDirection = (typeof Direction)[keyof typeof Direction];

const move = (direction: SafeDirection) => console.log(`Moving ${direction}`);
move(Direction.South);
move("south");
move("up"); // Argument of type '"up"' is not assignable to parameter of type 'SafeDirection'
```

对象类型推导 → 键名联合 → 值联合

* `as const` 用于强制将对象属性推断为字面量类型而非更宽泛的 `string` 类型且属性变为只读
* `keyof` 用于提取类型的所有键名，返回由它们组成的联合类型
* Type[Key]：索引访问类型